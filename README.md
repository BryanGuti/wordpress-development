# Docker Wordpress 🚀

Set up your Docker development environment

## Dockerfile

```Dockerfile
version: "3"

services:

    database:
      image: mysql:5.7
      container_name: <database_container_name>
      restart: always
      environment:
        MYSQL_ROOT_PASSWORD_FILE: /run/secrets/root_password
        MYSQL_DATABASE: <wordpress_database_name>
        MYSQL_USER: <wordpress_database_user>
        MYSQL_PASSWORD_FILE: /run/secrets/user_password
      secrets:
        - root_password
        - user_password

    phpmyadmin:
      depends_on:
        - database
      image: phpmyadmin/phpmyadmin:latest
      container_name: phpmyadmin
      restart: always
      environment:
        PMA_HOST: database
        PMA_USER: <wordpress_database_user>
        PMA_PASSWORD_FILE: /run/secrets/user_password
      ports:
        # - 8080:80
          - <phpmyadmin_local_port>:<phpmyadmin_container_port>
      secrets:
        - user_password

    wordpress:
      depends_on:
        - database
      image: wordpress:latest
      container_name: <wordpress_project_name>
      restart: always
      ports:
        # - 8000:80
        - <wordpress_local_port>:<wordpress_container_port>
      environment:
        WORDPRESS_DB_HOST: database:3306
        WORDPRESS_DB_USER: <wordpress_database_user>
        WORDPRESS_DB_PASSWORD_FILE: /run/secrets/user_password
        WORDPRESS_DB_NAME: <wordpress_database_name>
      volumes:
        - ./<wordpress_project_name>:/var/www/html
      secrets:
        - user_password

secrets:
  root_password:
    file: root_password.txt
  user_password:
    file: user_password.txt

volumes:
  mysql: {}

```

## Add writing permissions

```bash
sudo chmod -R o+w <wordpress_project_directory>
```