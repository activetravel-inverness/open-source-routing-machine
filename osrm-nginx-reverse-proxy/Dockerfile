###
# We use nginx as our reverse proxy, hiding walking and cycling
# directions behind 1 server.
###
from nginx:1.21.6

# Any static resources can be served from the normal web root.
copy ./usr/share/nginx/html /usr/share/nginx/html

# A custom nginx.conf, and associated files, replace the default
# configuration.
copy ./etc/nginx /etc/nginx
