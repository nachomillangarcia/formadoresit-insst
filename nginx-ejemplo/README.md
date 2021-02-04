
# Servidor Nginx en Docker

Para construir y ejecutar la app

```
docker build -t my-nginx .
docker run -d --name my-nginx -p 8080:80 my-nginx
```

Ahora puedes acceder a (http://localhost:8080)[http://localhost:8080]

Para montar la carpeta src dentro del contenedor y ver los cambios en tiempo real, para desarrollo: 

```
docker run -d --name my-nginx -p 8080:80 -v $(pwd)/src:/usr/share/nginx/html:ro my-nginx
```