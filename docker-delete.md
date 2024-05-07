# Desinstalar Docker

Para desinstalar completamente Docker:

#### Paso 1
```ssh
	dpkg -l | grep -i docker
```
#### Paso 2
```ssh
	sudo apt-get purge -y docker-engine docker docker.io docker-ce docker-ce-cli
	sudo apt-get purge docker-engine
  sudo apt-get purge docker-ce-cli
	sudo apt-get autoremove -y --purge docker-engine docker docker.io docker-ce  
  sudo apt-get autoremove --purge docker-engine
```
No eliminan imágenes, contenedores, volúmenes o archivos de configuración creados por el usuario en su host. 

#### Paso 3
Si desea eliminar todas las imágenes, contenedores y volúmenes, ejecute:
```ssh
	sudo rm -rf /var/lib/docker
  sudo rm -rf /var/lib/docker /etc/docker
	sudo rm -rf /var/run/docker.sock
  sudo rm /etc/apparmor.d/docker
	sudo rm /etc/docker
 	sudo rm /usr/local/bin/docker-compose
  sudo groupdel docker
	sudo rm ~ / .docker
	sudo find / -name '*docker*'
```

#### Paso 4
Comprobar entradas:
```ssh
sudo iptables -L
```

#### Paso 5 (Opcional y con cuidado)
Eliminar cadenas y reglas:
Copia de seguridad primero: 
```ssh
iptables-save > $ (date -I) -iptables.save
```
```ssh
	iptables-save | grep -v -i docker | iptables-restore
```

Ha eliminado Docker del sistema por completo.
