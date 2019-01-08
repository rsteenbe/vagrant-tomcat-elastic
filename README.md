# Vagrant Ubuntu - 2 Boxes - Tomcat 9 and Elastic Search 6

This box works on Vagrant 1.9.3.

Run `vagrant up --provider=virtualbox`.

Check in the browser if Tomcat and Elastic Search are working:
`http://localhost:8180` and `http://localhost:9200`

SSH to the boxes with:
`vagrant ssh tomcat` and `vagrant ssh elastic`