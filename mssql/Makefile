MSSQL_SA_PASSWORD=YOUR_STRONG_PASSWORD

.PHONY: all

all: server provision-db
# Start DB server using a Docker container
server:
	docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=${MSSQL_SA_PASSWORD}' \
		--name 'sql1' -p 1401:1433 \
		-d mcr.microsoft.com/mssql/server:2019-latest   

provision-db:
	docker cp provision-db.sql sql1:/var/opt/mssql/scripts
	docker exec -it sql1 /opt/mssql-tools/bin/sqlcmd \
  	-S localhost -U SA -P "${MSSQL_SA_PASSWORD}" \
	-i "/var/opt/mssql/scripts/provision-db.sql"
