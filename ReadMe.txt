Basic Commands https://rominirani.com/docker-tutorial-series-part-2-basic-commands-baaf70807fd3
	$ docker help
	$ docker COMMAND --help
	$ docker version
	$ docker info
	$ docker search busybox
	$ docker pull busybox
	$ docker run -t -i busybox 
		// same as $ docker run -t -i busybox:latest, -t: tty input, -i: interactive 
		# exit
	$ docker ps
	$ docker images
	$ docker ps -all
	$ docker start ab0e37214358
	$ docker attach ab0e37214358

More on Images and Containers https://rominirani.com/docker-tutorial-series-part-3-more-on-images-and-containers-68ce7a026fc1
	$ docker search httpd 
		// Apache Web Server
	$ docker run -d --name MyWebServer httpd
		// --name case senstive
	$ docker ps
	$ docker stop MyWebServer
	$ docker rm MyWebServer
	$ docker run -d --name MyWebServer httpd 
		// -d: Detached mode
	$ docker port MyWebServer
	$ docker stop MyWebServer
	$ docker rm MyWebServer
	$ docker run -d --name MyWebServer -P httpd
		//-P Publish all exposed ports to random ports
	$ docker port MyWebServer
		// 80/tcp -> 0.0.0.0:32768
	$ docker-machine ip default
		// http://192.168.99.100:32768
	$ docker stop MyWebServer
	$ docker rm MyWebServer
	$ docker run -d --name MyWebServer -p 80:80 httpd
		// 80/tcp -> 0.0.0.0:80
	
Docker Hub https://rominirani.com/docker-tutorial-series-part-4-docker-hub-b51fb545dd8e
	$ docker pull java
		// same as $ docker pull java:latest or $ docker pull java:8
	$ docker run java
	$ docker search mysql
	$ docker search java

Building your own Docker Images https://rominirani.com/docker-tutorial-series-part-5-building-your-own-docker-images-b4a448b44afc
	$ docker images
	$ docker pull ubuntu:latest
	$ docker run -it --name mycontainer1 --rm ubuntu:latest
		// --rm container is removed on termination
		root@e12d98fc354a:/#
		root@e12d98fc354a:/#apt-get update
		root@e12d98fc354a:/#apt-get install git
		root@e12d98fc354a:/#git --version
			// git version 2.17.1
		root@e12d98fc354a:/#exit
	$ docker run -it --name mycontainer1 --rm ubuntu:latest
		root@864244b97892:/# git
			// root@864244b97892:/# git
		root@e12d98fc354a:/#exit
	$ docker run -it --name mycontainer1 ubuntu:latest
		root@e12d98fc354a:/#apt-get update
		root@e12d98fc354a:/#apt-get install git
		root@e12d98fc354a:/#git --version
			// git version 2.17.1
		root@e12d98fc354a:/#exit
	$ docker commit mycontainer1 dadac76/ubuntu-git
	$ docker images
		// REPOSITORY           TAG                 IMAGE ID            CREATED              SIZE
		// dadac76/ubuntu-git   latest              4c14c9698c93        About a minute ago   206MB
	$ docker run -it --name c1 dadac76/ubuntu-git
	$ docker push dadac76/ubuntu-git
		// denied: requested access to the resource is denied
		// $ docker login
		// $ docker push dadac76/ubuntu-git
	$ docker search ubuntu-git

Docker Private Registry https://rominirani.com/docker-tutorial-series-part-6-docker-private-registry-15d1fd899255
	$ docker pull registry
	$ docker run -d -p 5000:5000 --name localregistry registry
	$ docker ps
	$ docker pull alpine
	$ docker pull localhost:5000/alpine
		// Using default tag: latest
		// Error response from daemon: manifest for localhost:5000/alpine:latest not found
	$ docker tag alpine:latest localhost:5000/alpine:latest
	$ docker push localhost:5000/alpine:latest
	$ docker run -it --name test --rm localhost:5000/busybox
		#ls
		#exit


Data Volumes https://rominirani.com/docker-tutorial-series-part-7-data-volumes-93073a1b5b72
	$ docker run -it -v /data --name container1 busybox
		# ls
		bin   data  dev   etc   home  proc  root  sys   tmp   usr   var
		# cd data
		data # touch file1.txt
		data # ls
		file1.txt
		data #
		data # exit
		$
	$ docker inspect container1
		"Destination": "/data",
		"Driver": "local",
	$ docker restart container1
	container1
	$ docker attach container1
		# ls
		bin   data  dev   etc   home  proc  root  sys   tmp   usr   var
		# cd data
		data # ls
		file1.txt
		data #
		data # exit
	$ docker rm container1
	container1
	$ docker volume ls

		Add d:\app to Virtualbox https://blog.shahinrostami.com/2017/11/docker-toolbox-windows-7-shared-volumes/
		$ docker-machine stop default // stop docker image in Virtualbox
			cd C:/Program Files/Oracle/VirtualBox
			VBoxManage.exe sharedfolder add default --name "app" --hostpath "\\?\d:\app" --automount
			-- not needed. VBoxManage.exe setextradata default VBoxInternal2/SharedFoldersEnableSymlinksCreate/v-root 1
			-- not needed. VBoxManage.exe setextradata default VBoxInternal2/SharedFoldersEnableSymlinksCreate/app 1
		$ docker-machine ssh default
			# sudo mkdir --parents /mnt/app
			# sudo mount -t vboxsf app /mnt/app // unmount: sudo umount -a -t vboxsf 
			# exit
			
	$ docker run -it --name container1 -v /app:/datavol busybox
		# cd datavol
		datavol # ls
		data # touch file1.txt
		data # touch file2.txt

	launch another terminal
	$  docker exec container1 ls /datavol
	file1.txt
	file2.txt

	$ docker run -it --volumes-from container1 --name container2 busybox
		# ls	
		# cd data
		data # touch file3.txt
		//check d:\app
	
Linking Containers https://rominirani.com/docker-tutorial-series-part-8-linking-containers-69a4e5bf50fb
	// see also https://docs.docker.com/compose/
	$ docker pull redis
	$ docker run -d --name redis1 redis
	$ docker run -it --link redis1:redis --name redisclient1 busybox
	# cat /etc/hosts
		// ff02::2 ip6-allrredis 5318408f8f95 redis1
	# ping redis
		// PING redis (172.17.0.3): 56 data bytes
		// 64 bytes from 172.17.0.3: seq=0 ttl=64 time=0.066 ms		
	# set
		// REDIS_ENV_GOSU_VERSION='1.10'
		// REDIS_ENV_REDIS_DOWNLOAD_SHA='e2
		// REDIS_ENV_REDIS_DOWNLOAD_URL='ht
		// REDIS_ENV_REDIS_VERSION='5.0.3'
		// REDIS_NAME='/redisclient1/redis'
		// REDIS_PORT='tcp://172.17.0.3:637
		// REDIS_PORT_6379_TCP='tcp://172.1
		// REDIS_PORT_6379_TCP_ADDR='172.17
		// REDIS_PORT_6379_TCP_PORT='6379'
		// REDIS_PORT_6379_TCP_PROTO='tcp'	
		
	$ docker run -it --link redis1:redis --name client1 redis sh
	# redis-cli -h redis
	redis:6379> ping
	redis:6379> set myvar DOCKER
	redis:6379> get myvar // "DOCKER"

	$ docker run -it --link redis1:redis --name client2 redis sh
	# redis-cli -h redis
	redis:6379> get myvar // "DOCKER"

Writing a Dockerfile https://rominirani.com/docker-tutorial-series-writing-a-dockerfile-ce5746617cd
	$ cd d:/app/images
	$ vi Dockerfile
		FROM busybox:latest
		MAINTAINER Tae Hee Choi (email@domain.com)
		CMD ["date"]
		// Esc + : + wq
	$ docker build -t myimage:latest .  //-t is the Docker image tag, ‘.’ specifies the location of the Dockerfile 
	$ docker run -it myimage	
		//Fri Feb 22 19:09:10 UTC 2019
	$ docker run -it myimage /bin/sh
		#
	$ vi Dockerfile
		FROM busybox:latest
		MAINTAINER Tae Hee Choi (email@domain.com)
		ENTRYPOINT ["cat"]
		CMD ["etc/passwd"]
		// Esc + : + wq
	$ docker run -it myimage
		// root:x:0:0:root:/root:/bin/sh
		// daemon:x:1:1:daemon:/usr/sbin:/bin/false
		// bin:x:2:2:bin:/bin:/bin/false
	


Docker Swarm Tutorial
https://rominirani.com/docker-swarm-tutorial-b67470cf8872

$ docker-machine create --driver virtualbox manager1 // create a new image on VM
$ docker-machine ls
	// NAME       ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER     ERRORS
	// default    *        virtualbox   Running   tcp://192.168.99.100:2376           v18.09.2
	// manager1   -        virtualbox   Running   tcp://192.168.99.101:2376           v18.09.2
$ docker-machine create --driver virtualbox worker1
$ docker-machine create --driver virtualbox worker2
$ docker-machine create --driver virtualbox worker3
$ docker-machine ls
	// NAME       ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER     ERRORS
	// default    *        virtualbox   Running   tcp://192.168.99.100:2376           v18.09.2
	// manager1   -        virtualbox   Running   tcp://192.168.99.101:2376           v18.09.2
	// worker1    -        virtualbox   Running   tcp://192.168.99.102:2376           v18.09.2
$ docker-machine ip manager1
	// 
$ docker-machine ssh manager1 // ssh into manager1
	docker@manager1:~$ docker swarm init --advertise-addr 192.168.99.101 // ip address from manager1
		// Swarm initialized: current node (84ki2iri1fqub7j4idk138e6t) is now a manager.
	docker@manager1:~$ docker node ls
		// ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
		// 84ki2iri1fqub7j4idk138e6t *   manager1            Ready               Active              Leader              18.09.2
	docker@manager1:~$ docker swarm join-token worker 
		// docker swarm join --token SWMTKN-1-535kpt7qf27dki98jopgvoiwzzdt4zortva0mgh88xkvm2euwj-5zu4tw4cer6glkt61b1ise63u 192.168.99.101:2377

Fire up other command terminals for worker1, worker2 and worker3
$ docker-machine ssh worker1
	docker@worker1:~$  docker swarm join --token SWMTKN-1-535kpt7qf27dki98jopgvoiwzzdt4zortva0mgh88xkvm2euwj-5zu4tw4cer6glkt61b1ise63u 192.168.99.101:2377
	// This node joined a swarm as a worker.
$ docker-machine ssh worker2
	docker@worker1:~$  docker swarm join --token SWMTKN-1-535kpt7qf27dki98jopgvoiwzzdt4zortva0mgh88xkvm2euwj-5zu4tw4cer6glkt61b1ise63u 192.168.99.101:2377
	// This node joined a swarm as a worker.
$ docker-machine ssh worker3
	docker@worker1:~$  docker swarm join --token SWMTKN-1-535kpt7qf27dki98jopgvoiwzzdt4zortva0mgh88xkvm2euwj-5zu4tw4cer6glkt61b1ise63u 192.168.99.101:2377
	// This node joined a swarm as a worker.

Back to manager1 ssh
	docker@manager1:~$ docker node ls
	// ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
	// 84ki2iri1fqub7j4idk138e6t *   manager1            Ready               Active              Leader              18.09.2
	// cxr4rpxcqpqt2qq7lfhcel9av     worker1             Ready               Active                                  18.09.2
	// swnnymfzjna41178g4gu76j4p     worker2             Ready               Active                                  18.09.2
	// p4l2maxaklw0v2ft89vy3b2pl     worker3             Ready               Active                                  18.09.2	
	docker@manager1:~$ docker info
	// Swarm: active
	//  NodeID: 84ki2iri1fqub7j4idk138e6t
	//  Is Manager: true
	//  ClusterID: knsd18damf96d5hizunityrjw
	//  Managers: 1
	//  Nodes: 4
	docker@manager1:~$ docker service create --replicas 3 -p 80:80 --name web nginx
	// pxs5alqfpnpee3csivxidmhja
	// overall progress: 3 out of 3 tasks
	// 1/3: running   [==================================================>]
	// 2/3: running   [==================================================>]
	// 3/3: running   [==================================================>]
	// verify: Service converged
	docker@manager1:~$ docker service ls
	// ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
	// pxs5alqfpnpe        web                 replicated          3/3                 nginx:latest        *:80->80/tcp
	docker@manager1:~$ docker service ps web
	// ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE           ERROR               PORTS
	// v4a7wsjm3n0m        web.1               nginx:latest        manager1            Running             Running 2 minutes ago
	// zqbm5tk6vwfr        web.2               nginx:latest        worker1             Running             Running 2 minutes ago
	// ynva1rlyb855        web.3               nginx:latest        worker2             Running             Running 2 minutes ago
	docker@manager1:~$ docker ps
	// CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
	// cb63c526b7f2        nginx:latest        "nginx -g 'daemon of."   5 minutes ago       Up 5 minutes        80/tcp              web.1.v4a7wsjm3n0mnmdv6zgxq8vmh

Web browser
	http://192.168.99.101 ~ http://192.168.99.104
	// put Docker Swarm service behind a Load Balancer

Back to manager1 ssh
	docker@manager1:~$ docker service scale web=8
	// verify: Service converged
	docker@manager1:~$ docker service ls
	// ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
	// pxs5alqfpnpe        web                 replicated          8/8                 nginx:latest        *:80->80/tcp
	// docker@manager1:~$
	docker@manager1:~$ docker service ps web
	// ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE                ERROR               PORTS
	// v4a7wsjm3n0m        web.1               nginx:latest        manager1            Running             Running 20 minutes ago
	// zqbm5tk6vwfr        web.2               nginx:latest        worker1             Running             Running 20 minutes ago
	// ynva1rlyb855        web.3               nginx:latest        worker2             Running             Running 20 minutes ago
	// 228mjcuu4vss        web.4               nginx:latest        worker2             Running             Running 2 minutes ago
	// j9ichgwygmk7        web.5               nginx:latest        worker3             Running             Running about a minute ago
	// u6sv4vvxpwd2        web.6               nginx:latest        worker3             Running             Running about a minute ago
	// yuc4do3nagzg        web.7               nginx:latest        manager1            Running             Running 2 minutes ago
	// urqjyxvka06i        web.8               nginx:latest        worker1             Running             Running 2 minutes ago

	docker@manager1:~$ docker node inspect self
	docker@manager1:~$ docker node inspect worker1 or docker@manager1:~$ docker node inspect —-pretty worker1

	docker@manager1:~$ docker node ps worker1
	// ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE            ERROR               PORTS
	// zqbm5tk6vwfr        web.2               nginx:latest        worker1             Running             Running 30 minutes ago
	// urqjyxvka06i        web.8               nginx:latest        worker1             Running             Running 11 minutes ago
	docker@manager1:~$ docker node update --availability drain worker1
	// worker1
	docker@manager1:~$ docker service ps web
	// ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE            ERROR               PORTS
	//v4a7wsjm3n0m        web.1               nginx:latest        manager1            Running             Running 34 minutes ago
	// nho1192hyjr4        web.2               nginx:latest        manager1            Running             Running 1 second ago
	// zqbm5tk6vwfr         \_ web.2           nginx:latest        worker1             Shutdown            Shutdown 5 seconds ago
	// ynva1rlyb855        web.3               nginx:latest        worker2             Running             Running 34 minutes ago
	// 228mjcuu4vss        web.4               nginx:latest        worker2             Running             Running 15 minutes ago
	// j9ichgwygmk7        web.5               nginx:latest        worker3             Running             Running 15 minutes ago
	// u6sv4vvxpwd2        web.6               nginx:latest        worker3             Running             Running 15 minutes ago
	// yuc4do3nagzg        web.7               nginx:latest        manager1            Running             Running 15 minutes ago
	// kaqdkpfcgpgh        web.8               nginx:latest        worker2             Running             Running 1 second ago
	// urqjyxvka06i         \_ web.8           nginx:latest        worker1             Shutdown            Shutdown 6 seconds ago
	docker@manager1:~$ docker service rm web
	// web
	// docker@manager1:~$ docker service ls
	// ID                  NAME                MODE                REPLICAS            IMAGE               PORTS










	
	



	
