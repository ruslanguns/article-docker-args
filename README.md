## Docker ARG y Variables de Entorno

### Introducción

En este repositorio tengo un breve ejemplo de cómo usar variables de entorno en el despliegue de contenedores con Docker, asimismo te enseñaré cómo configurarlas de forma dinámica.

### Inspiración

Esto nos trae un excelente beneficio para pasar argumentos con claves y configuraciones que preferiblemente deben ser secretas, o simplemente se desean pasar de forma dinámica. Al referirme con el término dinámico intento decir que puede que se necesite replicar o ascentar diferentes configuraciones / versiones para la misma imagen, excelente para cuando estamos en equipos de desarrollo.

### Modo de uso

Estos argumentos, se lo podemos pasar de diferentes maneras, lo único que debemos hacer es configurar una entrada en nuestro `Dockerfile` para que sepa que necesitamos que esté a la escucha de este argumento:

Ejemplo:

```bash
...
ARG NAME
RUN echo "Hola ${NAME}!"
...
```

En el ejemplo anterior estoy definiendo una variable con el argumento NAME y este lo estoy pintando en un comando RUN para que imprima en consola el típico "Hola \$NAME" siendo NAME la variable dinámica que estamos pasando desde las variables de entorno de nuestro sistema operativo anfitrión las cuales pueden reemplazadas cuando consumamos la imagen.

### El momento del build

#### Variables desde el comando del build

Si queremos ejecutar la imagen simplemente lanzamos el build con la bandera --build-arg <Nombre del argumento=valor_del argumento> ejemplo para este caso: `docker build --build-arg NAME=RUSLAN .`.

#### Variables desde el sistema operativo

Dependiendo del sistema operativo puede que tengamos que exportar la variable de entorno de diferentes formas pero al menos en LINUX es de la siguiente manera:

```bash
export $NAME=Ruslan
```

Posteriomente para decirle a docker que este arguemento se lo pasaremos dinámicamente simplemente no definimos ese valor, `docker build --build-arg NAME .` y él ya sabrá que debe estar a la escucha desde ese argumento.

> Tener en cuenta que si no hemos exportado la variable de entorno puede que dependiendo del fin de esa variable puede ser crucial para que la configuración se ejecute correctamente, pero siempre tener en cuenta que podemos hacer una buena práctica en colocar un valor por defecto desde el mismo Dockerfile, en caso que exista una variable con un valor por defecto Docker sabrá que queremos darle prioridad al argumento que hemos declarado mediante el comando del build por lo tanto éste será el valor definitivo de dicha variable y hará caso omiso del valor por defecto que hemos configurado en el archivo.

Pues hasta este punto ya sabemos cómo inicializar un valor y cómo dinamizarlo con las variables de entorno, ahora sabiendo que Docker es un sistema de contenedores de capas, nosotros también podemos modificar el valor de este argumento en el momento de consumir la imagen:

#### Desde el Docker Compose

> En caso que no conozcas sobre DOCKER COMPOSE te recomiendo vallas hagas click en [este enlace](https://docs.docker.com/compose/) para leer más sobre él.

Para consumir variables de entorno desde el docker compose simplemente definimos la opcion environment dentro de la configuración de nuestros servicios:

Ejemplo:

```yml
version: "3"
services:
  hola:
    build: .
    environment:
      NAME: "Ruslan desde Compose"
```

> Tener en cuenta que la sintaxis de los archivos yaml los obliga a tener indentaciones siempre. Por lo que mucho ojo al escribir aquí.

#### Desde la linea de comandos de Docker

Con Docker run podemos pasar variables de entorno mediante la bandera `-e`.

Ejemplo:
`docker run -e "NAME=RUSLAN" ruslanguns/hello`

### Conclusión

Creo que esta bastante claro que Docker es una pasada, y en mi caso estoy en constante evolución en mi aprendizaje con él. En este pequeño artículo puedo mostrarles cómo cubrir una necesidad grande sobre el manejo personalizado de Docker, espero les sea de utilidad.

### Referencias:

- [Docker ARG](https://docs.docker.com/engine/reference/builder/#arg)
- Article: [Docker ARG, ENV and .env - a Complete Guide](https://vsupalov.com/docker-arg-env-variable-guide/)
