# Docker devkit for developing Magento 2 projects #

### Base commands:
1. Connect to DB: 
```
bin/cli mysql -h db -umagento -pmagento magento
```
2. Import DB:
```
bin/clinotty mysql -h db -u root -pmagento magento < dbdump.sql 
```
3. Make DB Dump:
```
bin/clinotty mysqldump -hdb -uroot -pmagento magento | gzip > magento.sql.gz
```
4. Creating volume before start working
```
bin/create-volume magento_appdata | magento - is project name
```

### Project structure:

1. compose - the folder with docker files
1.1. compose/bin - the folder with main commands
1.2. env - the folder with Db configurations
1.3. images - internal images which using into docker
1.4. docker-compose.yml - main compose file
1.5. docker-compose.dev.yml - compose file with developer configs

2. html - the folder where must be places project sources

### Docker configurations:

1. change file env.sample to .env
2. in file .env fill PROJECT_NAME and PROJECT_HOST variables

### Configure PHPStorm Debugger

1. PhpStorm > Preferences > Languages & Frameworks > PHP
2. CLI Interpreter > ...
3. + > From Docker, ...
4. Select "Docker Compose" and select path to docker-compose.yml
5. Path Mappings: set mappings <local project dir> -> /var/www/html/
6. Docker containerL -v /<local project dir>/html/:/var/www/html/ -e serverName=docker
7. Tab Debug -> Xdebug -> Debug port -> 9001
8. Tab Debug -> DBGp Proxy -> IDE Key: PHPSTORM, Host: docker, Port: 9001
9. Add PHP Remote Debug > where set server name: docker and set path


### TODO:
- Add correct and full documentation
- Move all configs to .env file
- Optimize and Fix switching between projects 