NGINX CAN BE USED AS
--
- Web Server
- Gateway
- Reverse Proxy
- Caching
- Rate limit
- Host static sites
- Host multi sites
- Load balancer

![image](https://github.com/pavankumar0077/nginx/assets/40380941/62fc9931-958e-49dd-b37e-898165bd3229)

NGINX DOC REF : ``` https://nginx.org/en/docs/ ```

NOTE :
--
- IF we modify or added new configurations then we need to reload nginx
- Before that we need to validate the configurations, Use ``` sudo nginx -t ``` command. ( It will check for the syntax errors and let you know the conf is successful or not )
- Checking syntax is mandatary be'coz if we are not checking ``` sudo nginx -t ``` and directly reload the nginx server, Server will CRASH.
- Now we need to RELOAD NGINX after checking the syntax.. ``` sudo systemctl reload nginx ```.
- KEEP IN MIND, USE RELOAD NOT RESTART ( RESTART WILL stop and start the SERVER - WE WILL GET DOWNLOAD TIME AS WELL) ( RELOAD WILL SET THE NEW CONFIG FILES ) 

Basic nginx conf:
--
```
server { 
    listen 80 default_server;
    root /var/www/cafe;

    server_name _;       

    index index.html index.htm;

    location / {
      try_files $uri $uri/ =404;
     

    }


}

```
- Here we are making 80 as default_server if any other server block is not catching request, default server block will serve it.
- Here server_name is "_", that means, Our localhost, ip or _ means it will take all.
- in place of location we can give like api path for example location /api, or location /login (login limit rete) something like that. we can serve is seperately based on the location.
- In location even we can use REGULAR EXPRESSIONS like location ~ or we can use exact match like location =
- try_files $uri $uri/ =404; means for example if we have www.example1.com/products --- uri -- products. So nginx will first check with uri wiht root if it is not found then it will return 404 ERROR. in general if we don't have that route if get is type of error in most of the websites.


