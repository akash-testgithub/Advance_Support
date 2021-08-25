#1.Mini Playlist Total Configuration

Step-1: Go to GIT→ “nexus”--> Download “mini-playlist.zip”(check blip release and       
             mini playlist compatibility in order to download the correct mini-playlist 
             version)
Step-2: Open local terminal provide below command-
	  scp mini-playlist.zip ops@amagi.brickftp.com: (run this command in local)
  password: opsshare123
  
  Note: Inorder to copy local files to the production server and we need to run the 
  above commands


Step-3: Open “Oscar” for the required Customer(samuelgoldwyn) \
  and provide below command-
	  mkdir nexus (As we don’t have nexus folder in home dir)
	  cd nexus
  Mkdir mini-playlist
	  cd mini-playlist
	  scp ops@amagi.brickftp.com:/mini-playlist.zip
	  password: opsshare123
	  unzip mini-playlist.zip
	 
The above command will create a mini-playlist directory inside nexus in home directory
And add the index.html file
	
Now open the nginx.conf file 
Command to open the nginx.conf file from home dir=> vi nginx/conf/nginx.conf

Now comment the below command:

        location ~ /(.*)/nexus/([^/]+)/((.*)?) {
                      root "/home/www/nexus";
                      try_files $uri "/$2/$3" /$2/index.html =404;
        }
  
        location ~ /nexus/([^/]+)/((.*)?) {
                      root "/home/www/nexus";
                      try_files $uri "/$1/$2" /$1/index.html =404;
        }






And add the below commands just below:


         location ~ /(.*)/nexus/([^/]+)/([^/]+)/((.*)?) {
                        rewrite /(.*)/nexus/([^/]+)/([^/]+)/((.*)?) /nexus/$2/$3/$4;
                        proxy_pass http://nexus-ui-artifacts.s3-website.ap-south-1.amazonaws.com;
         }
  
         location ~ /nexus/([^/]+)/([^/]+)/((.*)?) {
                        proxy_intercept_errors on;
                        error_page 404 = /nexus/$1/$2/index.html;
                        proxy_pass http://nexus-ui-artifacts.s3-website.ap-south-1.amazonaws.com;
         }
  
         location ~ /janus-webrtc/(.*) {
                        proxy_pass http://$1;
                        proxy_http_version 1.1;
                        proxy_set_header Upgrade $http_upgrade;
                        proxy_set_header Connection "Upgrade";
                        proxy_read_timeout 86400s;
                        proxy_send_timeout 86400s;
         }


	
Step-4: Now open “setting.yml” for the Nexus Version Change
	Use command “vi blip/current/config/settings.yml”
	
	Comment the version & Write new version
		#version: v4.1.0
		version: v5.5.6
	And save(;wq)

Step-5: To Restart the nginx follow the following steps:

	cd blip/current
	touch tmp/restart.txt
	sudo service nginx restart
	Password: beefed0108

Step-6: Refresh the Cloudport for Miniplaylist
	  It will appear



