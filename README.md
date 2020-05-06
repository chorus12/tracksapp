# Dockerized Tracks GTD™ web app 
https://www.getontracks.org  
Version: **2.4.1**

## Run a simple install with sqlite
Just run `docker-compose run -p 3000:3000 tracks`  
Tracks is available on http://localhost:3000

## Just in case you want to build the image locally  
`docker-compose build`

## Setup to use mysql  
1. Set the root password for mysql in `.env` file  
2. Run just mysql and setup the database
```sh
docker-compose run db
docker exec -it tracksapp_db bash
mysql -u root -p 
```
Create a database  
```sql
create database tracks character set UTF8;
create user 'tracks'@'localhost' identified by 'your-password';
create user 'tracks'@'%' identified by 'your-password';
GRANT ALL PRIVILEGES ON tracks.* TO 'tracks'@'%' IDENTIFIED BY 'your-password' WITH GRANT OPTION;
GRANT ALL PRIVILEGES ON tracks.* TO 'tracks'@'localhost' IDENTIFIED BY 'your-password' WITH GRANT OPTION; 
```
Stop the mysql container.  

3. Change `database.yml` file in app folder - just update the password
4. Uncomment the volume in `docker-compose.yml` to change db connect
```yaml
    volumes:
      - app/database.yml:/app/config/database.yml
```
5. You are almost good to go - just need to migrate the database
```sh
# run the docker-compose
docker-compose up -d && docker-compose logs -f
# get inside the tracks container
docker exec -it tracksapp_tracks bash
# inside the container give the command 
bundle exec rake db:migrate RAILS_ENV=development
```

6. One more thing - generate `secret_token:` for `site.yml`  
`/app/config/site.yml` has a key `secret_token` for verifying the integrity of signed cookies.  
You can either generate with a command `rake secret` or with any other means.  
Next you can mount a site.yml to a container:  
```yaml
    volumes:
      - app/site.yml:/app/config/site.yml
```

7. For production use run application behind nginx reverse proxy. 

Enjoy being productive!
