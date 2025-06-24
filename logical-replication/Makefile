.PHONY:  create-tables data publication subscription  test-data  reset all

create-tables:
	docker compose exec -T postgres-main psql -U postgres -d movie_app_main < create-tables.sql
	docker compose exec -T postgres-replica psql -U postgres -d movies_app_replica < create-tables.sql
data:
	docker compose exec -T postgres-main psql -U postgres -d movie_app_main < data-seeding.sql
publication:
	docker compose exec postgres-main psql -U postgres -d movie_app_main -c "CREATE PUBLICATION authors_pub FOR TABLE authors;"

subscription:
	docker compose exec postgres-replica psql -U postgres -d movies_app_replica -c "\
	CREATE SUBSCRIPTION replica_sub \
	CONNECTION 'host=postgres-main port=5432 dbname=movie_app_main user=postgres password=12345' \
	PUBLICATION authors_pub;"
test-data:
	docker compose exec postgres-main psql -U postgres -d movie_app_main -c "\
	INSERT INTO authors (name) VALUES ('Pedro'); \
	INSERT INTO authors (name) VALUES ('Jose'); \
	INSERT INTO movies (name, author_id) VALUES ('Movie 4', (SELECT id FROM authors WHERE name = 'Jose'));"
reset:
	docker compose exec postgres-replica psql -U postgres -d movies_app_replica -c "DROP SUBSCRIPTION IF EXISTS replica_sub;"
	docker compose exec postgres-main psql -U postgres -d movie_app_main -c "DROP PUBLICATION IF EXISTS authors_pub;"


	docker compose exec postgres-main psql -U postgres -d movie_app_main -c "DROP SCHEMA public CASCADE; CREATE SCHEMA public;"
	docker compose exec postgres-replica psql -U postgres -d movies_app_replica -c "DROP SCHEMA public CASCADE; CREATE SCHEMA public;"


all: create-tables data publication subscription test-data
