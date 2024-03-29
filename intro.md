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
- Here server_name is "_", that means, Our localhost, ip or _ means it will take all. ( Like from any domain )
- in place of location we can give like api path for example location /api, or location /login (login limit rete) something like that. we can serve is seperately based on the location.
- In location even we can use REGULAR EXPRESSIONS like location ~ or we can use exact match like location =
- try_files $uri $uri/ =404; means for example if we have www.example1.com/products --- uri -- products. So nginx will first check with uri wiht root if it is not found then it will return 404 ERROR. in general if we don't have that route if get is type of error in most of the websites.

- So in general, We need to add domain.com.conf in the cond folder and in the config file in server_name we need to give same name like domain.com.conf
- Browser will check for http headers for the host, AT that time it will check and match both are same or not



FYI
--
- We can give name to github repo clone : Like this as well -- sudo git clone https://github.com/coder/code1/git code-base
- Here code-base is the name what i have given instead of we get name from the github repo.

# HOW NGINX LOOKS FOR NGINX CONF EXACTLY
- ``` sudo nginx -T ```
- - Use this command see how nginx actually looks for config in nginx perspective.


Nginx Authentication
--
## NOTE: IN SOME CASE IN OUR ORGANIZATAIONS WE NEED TO NEED ACCESS TO THE WEBAPPLICATION TO QA TEAMS OR CLIENT TO ACESS WEBAPPLICATION, EVEN BEFORE WEB APPLICAITON IS IN DEVELOPMENT STAGE.
- AT THAT TIME WE USE AUTHENTICATION TO ACCESS THE WEBSITE WITH USERNAME & PASSWORD.
- SO THAT NOT ALL USER WILL ACESS THE WEBSITE ONLY WHO HAS USERNAME AND PASSWORD WILL ACCESS IT LIKE QA OR DEV TEAM.

- ![image](https://github.com/pavankumar0077/nginx/assets/40380941/3f520d44-aa14-41cc-82f0-e99b7a52037f)

``` auth_basic "Under developoment site";```
``` auth_basic_user_file /etc/nginx/.htpasswd;```

- We need to add these lines and create .htpasswd
- WE CREATE THAT .htpasswd folder we can tools like opemssl or apache-tool utlizes
- SINCE OPENSSL IS INBUILD IN UBUNTU WE WILL USE IT.
- ```sudo sh -c "echo -n 'pavandevopsengineer:' >> /etc/nginx/.htpasswd"``` -- This is create a USERNAME
- file created and writed as well.
- TO CREATE PASSWORD ``` sudo sh -c "openssl passwd -apr1 >> /etc/nginx/.htpasswd" ```
- Verify password.
- Now reload the page and check it will ask for username and password.
- Once we have entered username and password it will be not ask again till you close the broswer and open again. ( Session will end if we close browser )

![image](https://github.com/pavankumar0077/nginx/assets/40380941/2f69a256-ef07-4478-8e25-121afa84c4c2)

- Here for example we don't want to stop access the website complete, we need to stop access for speciiic route then we can do like abot location / -- this is for all here we have mentioned auth_basic off, the above auth_basic is overridded here.
- for /admin we have made 404 error simple.

# REVERSE PROXY
- Without proxy
![image](https://github.com/pavankumar0077/nginx/assets/40380941/94da41ef-1e7f-4ca9-b102-caf3cb9367d5)

- with reverse proxy
![image](https://github.com/pavankumar0077/nginx/assets/40380941/16093271-38c6-43e4-b4ae-10c503e85217)

Config
--
![image](https://github.com/pavankumar0077/nginx/assets/40380941/2f604d75-79a8-4eff-82e3-64aed9a1e937)

- Here we are using upstream and proxypass directives for reverse proxy,

Load banacler:
--

![image](https://github.com/pavankumar0077/nginx/assets/40380941/9ed6bce6-0a91-4b78-8ddc-2ed77b3adafb)

- TO ADD LOAD BALANCER TO NGINX WE NEED TO CREATE NEW INSTANCE AND RUN THE APPLICATION IN DIFFERENT AS WE HAVE RUN THE APPLICATION IN 8000 SO IN DIFFERENT INSTANCE WE HAVE TO RUN IN 8001
- INSTAND OF LOCALHOST WE NEED TO PUT IP OF THE EC2 INSTANCE OR MACHINE WHICH WE ARE USING.
- BY DEFAULT NGINX USES ROUND-ROBIN ALGORITHM to send requests to two servers using load balanacer.
- So request goes like 8000 and 8001 it switches between these 2 ports.
- We have other algorithms as well in nginx for load balancing

304 ERROR
--
- IF we found error like 304 it is because of browser is caching our response.
- we need to make conf changes to not to cache the response.
- we need to add ``` add_header Cache-Control no-store ```

![image](https://github.com/pavankumar0077/nginx/assets/40380941/0f6bb308-ca90-48ba-aba4-ddc2918013ac)


### If we need CANARY TYPE OF DEPLOYMENT
- Like we need to send more request to one instance and less requests to another instance.
- ![image](https://github.com/pavankumar0077/nginx/assets/40380941/beea9781-5406-4a7f-b874-44fafd5df0b2)
- Here we have given weight=3 for one server, THAT MEANS 3 REQUESTS WILL TAKEN BY SERVER 1, AFTER THAT 1 REQUEST WILL BE TAKEN BY SERVER 2

### BACKUP SERVER
- SOMETIMES WE HAVE A BACKUP SERVER IF MAIN SERVER IS DOWN WE NEED BACKUP SERVER IMMEDIATELY
- ![image](https://github.com/pavankumar0077/nginx/assets/40380941/8a9046f4-ca20-419e-89bd-e0f0820ab2c0)
- Here server one 8000 -- Will work as a main server, server two 8001 will run as a backup server.

### IF CAN DOWN THE SERVER
- IN SOME CASE WE NEED TO DOWN THE SERVER WE CAN DO THAT BY ADDING DOWN
- ![image](https://github.com/pavankumar0077/nginx/assets/40380941/44904e79-390b-4005-a8dc-8a3ebf1a2607)

### 502 Bad Gateway Error
- That means our reqeust is going to nginx server by reverse proxy is getting issues
- LIke application is stop or got errors and application is down at that time we can see this error.



FYI
--
### MTLS -- TYEP OF TLS WHICH IS USED FOR SERVICES IN KUBERNETES 



- 
