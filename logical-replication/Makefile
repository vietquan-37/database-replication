.PHONY: data publication subscription  test-data all


data:
	docker compose exec -T postgres-main psql -U postgres -d movie_app_main < populate-data.sql


publication:
	docker compose exec postgres-main psql -U postgres -d movie_app_main -c "CREATE PUBLICATION all_changes_pub_two FOR ALL TABLES;"

subscription:
	docker compose exec postgres-replica psql -U postgres -d movies_app_replica -c "\
	CREATE SUBSCRIPTION replica_sub \
	CONNECTION 'host=postgres-main dbname=movie_app_main user=postgres password=12345' \
	PUBLICATION all_changes_pub_two;"
test-data:
	docker compose exec postgres-main psql -U postgres -d movie_app_main -c "\
	INSERT INTO authors (name) VALUES ('Pedro'); \
	INSERT INTO authors (name) VALUES ('Jose'); \
	INSERT INTO movies (name, author_id) VALUES ('Movie 4', (SELECT id FROM authors WHERE name = 'Jose'));"

all: data publication subscription test-data
