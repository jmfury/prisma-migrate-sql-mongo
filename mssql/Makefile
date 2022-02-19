MSSQL_SA_PASSWORD=YOUR_STRONG_PASSWORD

.PHONY: all

all: server setup prerestore restore
# Start DB server using a Docker container
server:
	docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=${MSSQL_SA_PASSWORD}' \
		--name 'sql1' -p 1433:1433 -h sql1 \
		-v sql1data:/var/opt/mssql \
		-d mcr.microsoft.com/mssql/server:2019-latest   

setup: # Make dir in container for backup & Copy bak to backup dir
	docker exec -it sql1 mkdir /var/opt/mssql/backup
	curl -L -o wwi.bak 'https://github.com/Microsoft/sql-server-samples/releases/download/wide-world-importers-v1.0/WideWorldImporters-Full.bak' && \
	docker cp wwi.bak sql1:/var/opt/mssql/backup

prerestore:
	sudo docker exec -it sql1 /opt/mssql-tools/bin/sqlcmd -S localhost \
	-U SA -P '${MSSQL_SA_PASSWORD}' \
	-Q 'RESTORE FILELISTONLY FROM DISK = "/var/opt/mssql/backup/wwi.bak"'

restore: 
	sudo docker exec -it sql1 /opt/mssql-tools/bin/sqlcmd \
   		-S localhost -U SA -P '${MSSQL_SA_PASSWORD}' \
   		-Q 'RESTORE DATABASE WideWorldImporters FROM DISK = "/var/opt/mssql/backup/wwi.bak" WITH MOVE "WWI_Primary" TO "/var/opt/mssql/data/WideWorldImporters.mdf", MOVE "WWI_UserData" TO "/var/opt/mssql/data/WideWorldImporters_userdata.ndf", MOVE "WWI_Log" TO "/var/opt/mssql/data/WideWorldImporters.ldf", MOVE "WWI_InMemory_Data_1" TO "/var/opt/mssql/data/WideWorldImporters_InMemory_Data_1"'

verify: 
	sudo docker exec -it sql1 /opt/mssql-tools/bin/sqlcmd \
   		-S localhost -U SA -P '${MSSQL_SA_PASSWORD}' \
   		-Q 'SELECT Name FROM sys.Databases'
