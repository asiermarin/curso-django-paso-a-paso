# Curso de iniciación a Django paso a paso

Este es un curso diseñado para aprender desarrollar aplicaciones web con **Django desde cero**. Siguiendo la metodología *Learn by doing* está pensado para que vayas aprendiendo los fundamentos básicos de Django mientras construyes tu primera aplicación web.

## Conocimientos previos

El curso requiere unos conocimientos básicos de **Python, HTML y CSS**.
Del mismo modo partirá de la base de que tienes **Python, PIP y virtualenvwrapper** instalado en el sistema.

## Hands on!

The file explorer is accessible using the button in left corner of the navigation bar. You can create a new file by clicking the **New file** button in the file explorer. You can also create folders by clicking the **New folder** button.

### PASO 1: Instala Django en un entorno virtual 

Crear el entorno virtual
    
      mkvirtualenv env1

 Activar el entorno
    
      workon curso-django
    
    
Instalar Django

      pip install django


### PASO 2: Crea tu primer proyecto

Crea el proyecto Django:

      django-admin startproject curso-django
    
    
   La estructura de ficheros resultante es la siguiente:
    
    ```
     project/
         manage.py
         project/
     	__init__.py
     	settings.py
     	urls.py
     	wsgi.py
    
    ```
    
   Inicia el servidor con el comando `python manage.py runserver` dentro del directorio del proyecto.

### PASO 3: Crea tu primera aplicación
Posicionate en el directorio del proyecto `$ cd <outer_project_folder>`

Crea la aplicación ejecutando el siguiente comando:

`python manage.py startapp app-curso-django`

Dentro del directorio de la aplicación, crear un fichero llamado `urls.py` que utilizaremos para mapear las URLs de nuestra aplicación.

La estructura de ficheros resultante será la siguiente:

```
 project/
     manage.py
     db.sqlite3
     project/
 	__init__.py
 	settings.py
 	urls.py
 	wsgi.py
     app/
 	migrations/
 	    __init__.py
 	__init__.py
 	admin.py
 	apps.py
 	models.py
 	tests.py
 	urls.py
 	views.py

```

Incluir a aplicación dentro del fichero `settings.py` del proyecto, añadiendo el nombre a la lista `INSTALLED_APPS`:
	
	 INSTALLED_APPS = [
	 	'app-curso-django',
	 	# ...
	 ]

### PASO 4: Crea una nueva vista y configura su ruta de acceso

Dentro del directorio de la app, abrir `views.py` y añadir lo siguiente:

```python
from django.http import HttpResponse

def index(request):
    return HttpResponse("Listado de departamentos")
```
    
Abrir (o crear) el fichero `urls.py` y añadir el patrón para la siguiente ruta:
   ```python
    from django.urls import path
    from . import views
   
    urlpatterns = [
        path('', views.index, name='index'),
    ]
   ```
Dentro del directorio del proyecto, editar `urls.py` para incluir la redirección al fichero `urls.py` de la alpicación:
  ```python 
    urlpatterns = [
        path("", include('app-curso-django.urls')),
    ]
   ```

El resultado debe ser el siguiente:
   ```python
   from django.contrib import admin
   from django.urls import include, path
  
   urlpatterns = [
       path('app/', include('app.urls')),
       path('admin/', admin.site.urls),
   ]
   ```

De este modo tenemos un `urls.py` en cada directorio de nuestras aplicaciones gestionados desde el `urls.py` del directorio del proyecto.

### PASO 5: Crea el modelo de nuestra aplicación

Editar el fichero `models.py` de la aplicación creando las clases Empresa y Trabajador:

   ```python
     from django.db import models
    
     class Departamento(models.Model):
        # No es necesario crear un campo para la Primary Key, Django creará automáticamente un IntegerField.
        nombre = models.CharField(max_length=50)
        telefono = models.IntegerField()
    
     class Empleado(models.Model):
        # Campo para la relación one-to-many
        departamento = models.ForeignKey(Departamento, on_delete=models.CASCADE)
        nombre = models.CharField(max_length=40)
        fecha_nacimiento = models.DateField()
        # Es posible indicar un valor por defecto mediante 'default'
        antiguedad = models.IntegerField(default=0)
        
```    
Aplica los cambios realizados en el modelo mediante los siguientes comandos:
    
   ```
    $ python manage.py makemigrations <app_name>
    $ python manage.py migrate
   
   ```

### PASO 6: Añade y consulta registros desde la API de Django

Accede al intérprete interactivo de Django escribiendo el siguiente comando:

	python manage.py shell

Una vez dentro, ya puedes comenzar a crear y consultar registros.
   ```python
	>>> from djangoapp.models import Departamento, Empleado
	>>> departamento = Departamento(nombre="Oficina Tecnica", telefono="945010101")
	>>> departamento .save()
	# Django le asigna un id.
	>>> departamento.id
	1
	
	# Acceder a sus atributos
	>>> departamento.nombre
	'Oficina Tecnica'
	
	>>> Departamento.objects.all()
	<QuerySet [<Departamento: Departamento object (1)>]>
	
	>>> Departamento.objects.filter(nombre__contains='Oficina').count()
	1
	
	# Mostrar los empleados de un departamento.
	>>> departamento.empleado_set.all()
	<QuerySet []>
	
	# Crear empleados.
	>>> departamento.empleado_set.create(nombre='Mikel Uriarte', fecha_nacimiento='1985-01-23')
	<Empleado: Empleado object (1)>
	
	>>>> departamento.empleado_set.create(nombre='Ane Labastida', fecha_nacimiento='1987-12-14')
	<Empleado: Empleado object (2)>
	
	empleado = departamento.empleado_set.create(nombre='Luken Abarra', fecha_nacimiento='1989-05-12')
	
	# Es posible acceder desde el hijo a su padre.
	>>> empleado.departamento
	<Departamento: Departamento object (1)>
	
	>>> empleado.departamento.nombre
	'Oficina Tecnica'
	
	# También es posible acceder desde el padre a todos sus hijos.
	>>> departamento.empelado_set.all()
	<QuerySet [<Empleado: Empleado object (1)>, <Empleado: Empleado object (2)>, <Empleado: Empleado object (3)>]>
	
	>>>> departamento.empelado_set.count()
	3
   ```

Podemos añadir el método ```__str__()``` que será invocado al llamar a los métodos *print* o *str* de nuestros objetos. De esta forma podremos identificarlos más fácilmente.

```python
def __str__(self):
        return self.nombre
```

### PASO 7: Utiliza la aplicación de Administración de Django para visualizar y añadir registros

La aplicación de administración permite visualizar la información de nuestros modelos de forma sencilla. Crear un usuario para la aplicación de Administración
    
    ```
     python manage.py createsuperuser
    
    ```
    
Iniciar el servidor y entrar
    
   ```
    python manage.py runserver
   ```
    
Entrar en la aplicación [http://127.0.0.1:8000/admin](http://127.0.0.1:8000/admin)

### PASO 8: Crea las vistas y urls para interactuar con el modelo

Creamos las vistas correspondientes a las siguientes URLs:
| URL | Descripción de la vista |
|--|--|
|/app-curso-django/  | Muestra todos los departamentos  |
|/app-curso-django/<:id>  | Muestra los detalles de un departamento a partir del ID indicado  |
|/app-curso-django/<:id>/empleados  | Muestra los empleados del departamento con el ID indicado  |

Creamos las vistas (cada vista estará definida por una función):
```python
from django.shortcuts import render
from .models import Departamento, Empleado

#devuelve el listado de empresas
def index(request):
	departamentos = Departamento.objects.order_by('nombre')
	output = ', '.join([d.nombre for d in departamentos])
	return HttpResponse(output)

#devuelve los datos de un departamento
def detail(request, departamento_id):
	departamento = Departamento.objects.get(pk=departamento_id)
	output = ', '.join([departamento.id, departamento.nombre, departamento.telefono])
	return HttpResponse(output)

#devuelve los empelados de un departamento
def empleados(request, departamento_id):
	departamento = Departamento.objects.get(pk=departamento_id)
	output = ', '.join([e.nombre for e in departamento.empelado_set.all()])
	return HttpResponse(output)
```

### PASO 9: Mejora las vistas controlando errores 404

Utiliza el método `get_object_or_404` para controlar los casos en los que el registro de la petición no exista:

```python
from .models import Departamento, Empleado

#devuelve el listado de empresas
def index(request):
	departamentos = get_list_or_404(Departamento.objects.order_by('nombre'))
	output = ', '.join([d.nombre for d in departamentos])
	return HttpResponse(output)

#devuelve los datos de un departamento
def detail(request, departamento_id):
	departamento = get_object_or_404(Departamento, pk=departamento_id)
	output = ', '.join([departamento.id, departamento.nombre, departamento.telefono])
	return HttpResponse(output)

#devuelve los empelados de un departamento
def empleados(request, departamento_id):
	departamento = get_object_or_404(Departamento, pk=departamento_id)
	output = ', '.join([e.nombre for e in departamento.empelado_set.all()])
	return HttpResponse(output)
```


### PASO 10: Utiliza plantillas para mostrar la información

Actualiza las vistas creadas en `views.py`:

```python
from django.shortcuts import render
from .models import Departamento, Empleado

#devuelve el listado de empresas
def index(request):
	departamentos = get_list_or_404(Departamento.objects.order_by('nombre'))
	context = {'lista_departamentos': departamentos }
	return render(request, 'app-curso-django/index.html', context)

#devuelve los datos de un departamento
def detail(request, departamento_id):
	departamento = get_object_or_404(Departamento, pk=departamento_id)
	context = {'departamento': departamento }
	return render(request, 'app-curso-django/detail.html', context)

#devuelve los empelados de un departamento
def empleados(request, departamento_id):
	departamento = get_object_or_404(Departamento, pk=departamento_id)
	empleados =  departamento.empelado_set.all()
	context = {'departamento': departamento, 'empelados' : empelados }
	return render(request, 'app-curso-django/empleados.html', context)
```

Crea las plantillas que definan la estructura de las páginas HTML resultantes:

```python
#index.html:
<h1>Gestor de Empeleados</h1>
<h2>Listado de departamentos</h2>

{% if lista_departamentos %}
	<ul>
	{% for d in lista_departamentos %}
		<li>
		    <a href="/polls/{{ d.id }}/">{{ d.nombre}}</a>
		</li>
	{% endfor %}
	</ul>
{% else %}
<p>No hay departamentos creados.</p>
{% endif %}
```


```python
#detail.html:
<h1>Gestor de Empeleados</h1>
<h2>Datos del departamento</h2>

{% if departamento %}
    <ul>
        <li>
            {{ departamento.nombre}}
        </li>
        <li>
            {{ departamento.telefono}}
        </li>
        <li>
            <a href="/app-curso-django/{{ departamento.id }}/empleados">Ver empleados</a>
        </li>
    </ul>
{% else %}
    <p>No hay departamentos creados.</p>
{% endif %}
```

```python
#empelados.html:
<h1>Gestor de Empeleados</h1>
<h2>Lista de empleados</h2>
<h3>{{ departamento.nombre }}</h3>
{% if empelados %}
	<ul>
	{% for e in empleados %}
		<li>
		    {{ e.nombre}}
		</li>
	{% endfor %}
	</ul>
{% else %}
<p>No hay departamentos creados.</p>
{% endif %}
```



### PASO 11: La herencia en plantillas

Django permite crear una plantilla base que contenga el “esqueleto” con todos los elementos comunes y definir bloques que puedan sobreescritos por las plantillas que la hereden. 

Crea una plantilla base:
```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <link rel="stylesheet" href="style.css">
        <title>{% block titulo %}Gestor de Empleados {% endblock %}</title>
    </head>
    <body>
        <div id="content">
	     <h1>Gestor de Empeleados</h1>
            {% block contenido %}{% endblock %}
        </div>
    </body>
</html>
```


Creas las plantillas específicas que hereden de la plantilla base:

```python
#index.html:
{% extends "base.html" %}

{% block titulo %}Listado de departamentos{% endblock %}

{% block contenido %}
<h2>Listado de departamentos</h2>
{% if lista_departamentos %}
	<ul>
	{% for d in lista_departamentos %}
		<li>
		    <a href="/polls/{{ d.id }}/">{{ d.nombre}}</a>
		</li>
	{% endfor %}
	</ul>
{% else %}
<p>No hay departamentos creados.</p>
{% endif %}
{% endblock %}

```

### PASO 12: Actualizar las URLs

En lugar de utilizar la ruta de la URL, podemos utilizar el nombre que le hemos dado en el mapeo definido en `urls.py`

Antes:
```html
<li><a href="/miApp/{{ empresa.id }}/">{{ empresa.nombre }}</a></li>
```
Ahora
```html
<li><a href="{% url ‘detalle’ empresa.id%}">{{ empresa.nombre}}</a></li>
```
