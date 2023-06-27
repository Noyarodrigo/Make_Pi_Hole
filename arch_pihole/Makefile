help:
	@echo '+ install   -> docker & compose'
	@echo '+ run       -> compose up -> start pihole'
	@echo '+ stop      -> compose down'
	@echo '+ configure -> change DNS config to pihole'
	@echo '+ goback    -> undo DNS config'
	@echo '+ clean     -> (todo) undo config, delete pihole image'
	@echo '+ purge     -> (todo) clean and uninstall docker & compose'
	
install: 
	sudo pacman -S --noconfirm docker docker-compose
	sudo systemctl start docker
	sudo systemctl enable docker

run: pihole.yaml
	sudo sed -i "s/#DNSStubListener=/DNSStubListener=no/" /etc/systemd/resolved.conf
	sudo systemctl restart systemd-resolved.service
	sudo docker-compose -f pihole.yaml up -d

configure:
	@CONTAINER_IP=$$(docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' pihole); \
	echo 'Changing DNS to container_ip: '$$CONTAINER_IP; \
	sudo sed -i "s/^#DNS=/DNS=$$CONTAINER_IP/" /etc/systemd/resolved.conf
	sudo systemctl restart systemd-resolved.service

goback:
	sudo sed -i "s/^DNSStubListener=no/#DNSStubListener=/" /etc/systemd/resolved.conf
	sudo sed -i "/^DNS=/c\#DNS=" /etc/systemd/resolved.conf

	@CONTAINER_IP=$$(docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' pihole); \
	echo 'Restoring DNS conf'; \
	sudo systemctl restart systemd-resolved.service

stop: pihole.yaml goback
	sudo docker-compose -f pihole.yaml down
