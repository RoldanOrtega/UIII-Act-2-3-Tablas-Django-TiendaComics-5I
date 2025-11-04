# Lista corregida y ordenada — **Proyecto: Tienda de Comics (Django / Python / VS Code)**

A continuación tienes la lista revisada, corregida y puntuada con procedimientos, estructura y el `models.py` corregido (con `Proveedor` antes de `Producto` porque `Producto` lo referencia). He mantenido tus ideas y nombres, pero arreglé errores de sintaxis, identación y añadí las instrucciones exactas de comandos donde correspondía.

---

## 1 — Crear la carpeta del proyecto

1. Crear carpeta principal: `UIII_Tienda_Comics_0679` (evita espacios en el nombre para mayor compatibilidad).

## 2 — Abrir VS Code en la carpeta del proyecto

2. Abrir VS Code y **Abrir carpeta** → seleccionar `UIII_Tienda_Comics_0679`.

## 3 — Abrir terminal en VS Code

3. Abrir terminal integrada: `Terminal` → `New Terminal` (o Ctrl+`).

## 4 — Crear entorno virtual `.venv` desde terminal

4. En la terminal, dentro de la carpeta del proyecto:

* Windows (PowerShell / CMD):

  ```bash
  python -m venv .venv
  ```
* macOS / Linux:

  ```bash
  python3 -m venv .venv
  ```

## 5 — Activar el entorno virtual

5. Activar el entorno:

* Windows (PowerShell):

  ```powershell
  .\.venv\Scripts\Activate.ps1
  ```
* Windows (CMD):

  ```cmd
  .\.venv\Scripts\activate
  ```
* macOS / Linux:

  ```bash
  source .venv/bin/activate
  ```

## 6 — Seleccionar/activar intérprete de Python en VS Code

6. En VS Code: `Ctrl+Shift+P` → escribir **Python: Select Interpreter** → elegir el intérprete de `.venv` (aparecerá como `.venv`).

## 7 — Instalar Django

7. Con el entorno activado:

```bash
pip install django
```

## 8 — Crear proyecto `backend_Comics` sin duplicar carpeta

8. Para crear el proyecto **sin** crear una carpeta extra (evitar duplicar estructura), situarse en la raíz y ejecutar:

```bash
django-admin startproject backend_Comics .
```

> Nota: el punto `.` al final hace que los archivos del proyecto se creen en la carpeta actual y no dentro de `backend_Comics/backend_Comics`.

## 9 — Ejecutar servidor en el puerto 8079

9. Ejecutar:

```bash
python manage.py runserver 8079
```

## 10 — Copiar y pegar el link en el navegador

10. Abrir navegador y pegar:

```
http://127.0.0.1:8079/
```

(o `http://localhost:8079/`)

## 11 — Crear aplicación `app_Comics`

11. Crear la app:

```bash
python manage.py startapp app_Comics
```

## 12 — `models.py` (corregido)

12. Archivo `app_Comics/models.py` — aquí el código corregido y ordenado (definí `Proveedor` antes de `Producto`, corregí identación y añadí el campo `isbn` y la relación `ForeignKey` como pediste):

```python
from django.db import models

# -----------------------
# MODELO PROVEEDOR
# -----------------------
class Proveedor(models.Model):
    nombre_empresa = models.CharField(max_length=100)
    nombre_contacto = models.CharField(max_length=100)
    telefono_contacto = models.CharField(max_length=15)
    email_contacto = models.EmailField(unique=True)
    direccion = models.CharField(max_length=255)
    ciudad = models.CharField(max_length=100)
    pais = models.CharField(max_length=100)
    fecha_registro = models.DateField(auto_now_add=True)

    def __str__(self):
        return f"{self.nombre_empresa} - {self.nombre_contacto}"


# -----------------------
# MODELO PRODUCTO
# -----------------------
class Producto(models.Model):
    nombre = models.CharField(max_length=150)
    autor = models.CharField(max_length=100)
    editorial = models.CharField(max_length=100)
    genero = models.CharField(max_length=50)
    precio = models.DecimalField(max_digits=10, decimal_places=2)
    stock = models.IntegerField()
    fecha_publicacion = models.DateField()
    isbn = models.CharField(max_length=13, unique=True)
    tipo_producto = models.IntegerField()  # puedes cambiar a CharField/choices si quieres
    proveedor = models.ForeignKey(Proveedor, on_delete=models.CASCADE, related_name='productos')

    def __str__(self):
        return f"{self.nombre} - {self.autor}"


# ==========================================
# MODELO: CLIENTE (pendiente, definido pero trabajarás solo Producto por ahora)
# ==========================================
class Cliente(models.Model):
    producto = models.ForeignKey(Producto, on_delete=models.CASCADE, related_name='clientes')
    nombre = models.CharField(max_length=100)
    email = models.EmailField(unique=True)
    telefono = models.CharField(max_length=15)
    direccion = models.CharField(max_length=255)
    pais = models.CharField(max_length=100)
    ciudad = models.CharField(max_length=100)
    fecha_registro = models.DateField(auto_now_add=True)

    def __str__(self):
        return self.nombre
```

## 12.5 — Realizar migraciones (`makemigrations` y `migrate`)

12.5. Comandos:

```bash
python manage.py makemigrations
python manage.py migrate
```

## 13 — Empezamos trabajando con el modelo `Producto`

13. Primero desarrollar CRUD para `Producto` (dejar `Proveedor` y `Cliente` pendientes como dijiste).

## 14 — Vistas en `app_Comics/views.py` (funciones)

14. Crear funciones vistas (sencilla estructura sin forms.py ni validaciones):

* `inicio_comics(request)`
* `agregar_producto(request)`
* `actualizar_producto(request, producto_id)`
* `realizar_actualizacion_producto(request, producto_id)` *(o combinar con `actualizar_producto` según tu preferencia)*
* `borrar_producto(request, producto_id)`

Ejemplo de esqueleto (muy básico, sin validación):

```python
from django.shortcuts import render, get_object_or_404, redirect
from .models import Producto, Proveedor

def inicio_comics(request):
    return render(request, 'inicio.html')

def agregar_producto(request):
    if request.method == 'POST':
        # leer campos de request.POST y crear Producto (sin validaciones)
        Producto.objects.create(
            nombre=request.POST.get('nombre'),
            autor=request.POST.get('autor'),
            editorial=request.POST.get('editorial'),
            genero=request.POST.get('genero'),
            precio=request.POST.get('precio') or 0,
            stock=request.POST.get('stock') or 0,
            fecha_publicacion=request.POST.get('fecha_publicacion'),
            isbn=request.POST.get('isbn'),
            tipo_producto=request.POST.get('tipo_producto') or 0,
            proveedor=Proveedor.objects.first()  # ejemplo mínimo; ajusta según tu formulario
        )
        return redirect('ver_productos')
    return render(request, 'agregar_Productos.html')

def ver_productos(request):
    productos = Producto.objects.all()
    return render(request, 'Producto/ver_Productos.html', {'productos': productos})

def actualizar_producto(request, producto_id):
    producto = get_object_or_404(Producto, id=producto_id)
    if request.method == 'POST':
        # actualizar campos
        producto.nombre = request.POST.get('nombre')
        # ... actualizar otros campos ...
        producto.save()
        return redirect('ver_productos')
    return render(request, 'Producto/actualizar_Productos.html', {'producto': producto})

def borrar_producto(request, producto_id):
    producto = get_object_or_404(Producto, id=producto_id)
    if request.method == 'POST':
        producto.delete()
        return redirect('ver_productos')
    return render(request, 'Producto/borrar_Productos.html', {'producto': producto})
```

## 15 — Crear carpeta `templates` dentro de `app_Comics`

15. Estructura:

```
app_Comics/
  templates/
    base.html
    header.html
    navbar.html
    footer.html
    inicio.html
    Producto/
      agregar_Productos.html
      ver_Productos.html
      actualizar_Productos.html
      borrar_Productos.html
```

## 16 — Archivos HTML en `templates` (nombres)

16. Crear: `base.html`, `header.html`, `navbar.html`, `footer.html`, `inicio.html`.

## 17 — `base.html` — agregar Bootstrap (CSS y JS)

17. Incluir CDN de Bootstrap en `base.html` (head y scripts al final). Ejemplo:

```html
<!-- head -->
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.4.0/dist/css/bootstrap.min.css" rel="stylesheet">
<!-- antes de cerrar body -->
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.4.0/dist/js/bootstrap.bundle.min.js"></script>
```

## 18 — `navbar.html` — opciones y submenus

18. Incluir opciones principales (con iconos en las opciones principales, no en submenus):

* “Sistema de Administración Comics”
* “Inicio”
* “Productos” → submenú: (Agregar Productos, Ver Productos, Actualizar Productos, Borrar Productos)
* “Proveedores” → submenú: (Agregar, Ver, Actualizar, Borrar)
* “Cliente” → submenú: (Agregar, Ver, Actualizar, Borrar)

(Usa `navbar` de Bootstrap y `dropdown` para submenús; añade iconos solo en los items principales con `<i class="...">` si usas FontAwesome).

## 19 — `footer.html`

19. Footer fijo al final con derechos y autor:

```html
<footer class="fixed-bottom text-center py-2 bg-light">
  &copy; {{ now.year }} - Creado por Andrea Roldan 5I, Cbtis 128
</footer>
```

(En el contexto de Django puedes usar `{{ now.year }}` si pasas `now` o usar template tag `now`).

## 20 — `inicio.html`

20. Página de inicio con descripción del sistema y una imagen tomada de la red sobre comics (usa `<img src="..." alt="">` con URL pública).

## 21 — Subcarpeta `Producto` dentro de `templates`

21. Ya indicada arriba; colocar los templates específicos de Producto allí.

## 22 — Archivos HTML de producto (detallados)

22. Dentro `app_Comics/templates/Producto/`:

* `agregar_Productos.html` (form básico con method POST)
* `ver_Productos.html` (tabla con botones **Ver**, **Editar**, **Borrar**)
* `actualizar_Productos.html`
* `borrar_Productos.html` (confirmación)

## 23 — No utilizar `forms.py`

23. Se respetará: manipulación directa con `request.POST` en vistas.

## 24 — Crear `urls.py` en `app_Comics`

24. Archivo `app_Comics/urls.py` ejemplo:

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_comics, name='inicio_comics'),
    path('productos/', views.ver_productos, name='ver_productos'),
    path('productos/agregar/', views.agregar_producto, name='agregar_producto'),
    path('productos/editar/<int:producto_id>/', views.actualizar_producto, name='actualizar_producto'),
    path('productos/borrar/<int:producto_id>/', views.borrar_producto, name='borrar_producto'),
]
```

## 25 — Agregar `app_Comics` en `settings.py`

25. En `backend_Comics/settings.py` → `INSTALLED_APPS` añadir:

```python
'...other apps...',
'app_Comics',
```

## 26 — Configurar `urls.py` del proyecto para enlazar `app_Comics`

26. En `backend_Comics/urls.py`:

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Comics.urls')),  # apunta a la app
]
```

## 27 — Registrar modelos en `admin.py` y migrar de nuevo

27. En `app_Comics/admin.py`:

```python
from django.contrib import admin
from .models import Producto, Proveedor, Cliente

admin.site.register(Producto)
admin.site.register(Proveedor)
admin.site.register(Cliente)
```

Luego:

```bash
python manage.py makemigrations
python manage.py migrate
```

> Nota: trabajaremos por ahora sólo con `Producto`. `Proveedor` y `Cliente` quedan pendientes según tu instrucción, pero los modelos ya están definidos para registro y futura migración.

## 28 — Estética y validación

28. * **Colores**: usar paleta de colores suaves y moderna (pasteles suaves).
    * **Simplicidad**: páginas sencillas y claras.
    * **No validar entrada de datos** (tal como solicitaste).

## 29 — Crear estructura completa al inicio

29. Al inicio crea toda la estructura de carpetas y archivos listada en esta guía para que el flujo de trabajo sea claro.

## 30 — Proyecto totalmente funcional (objetivo)

30. Objetivo: CRUD funcional para `Producto`, plantilla base y navegación, servidor corriendo en puerto 8079.

## 31 — Ejecutar servidor en puerto 8079 (final)

31. Comando final:

```bash
python manage.py runserver 8079
```

---

### Recursos de plantilla rápida (snippets útiles)

**Comando para crear superusuario (para acceder a admin):**

```bash
python manage.py createsuperuser
```

**Ejemplo de tabla en `ver_Productos.html`:**

```html
<table class="table">
  <thead><tr><th>Nombre</th><th>Autor</th><th>Precio</th><th>Stock</th><th>Acciones</th></tr></thead>
  <tbody>
    {% for p in productos %}
    <tr>
      <td>{{ p.nombre }}</td>
      <td>{{ p.autor }}</td>
      <td>{{ p.precio }}</td>
      <td>{{ p.stock }}</td>
      <td>
        <a href="{% url 'actualizar_producto' p.id %}" class="btn btn-sm btn-primary">Editar</a>
        <a href="{% url 'borrar_producto' p.id %}" class="btn btn-sm btn-danger">Borrar</a>
      </td>
    </tr>
    {% endfor %}
  </tbody>
</table>
```

---

Si quieres, puedo:

* Generar los archivos `views.py`, `urls.py`, y las plantillas HTML completas (con código listo para pegar) para que tengas un proyecto inicial funcional.
* O puedo preparar un `README.md` con todos los comandos paso a paso listos para copiar/pegar.

Dime cuál prefieres y lo entrego ya (no pediré esperar ni ejecutar nada por ti).
