# Proyecto Tienda de Cómics - Django

## 1. Crear carpeta del proyecto
```bash
mkdir UIII_Tienda_Comics_0679
```

## 2. Abrir VS Code en la carpeta
```bash
cd UIII_Tienda_Comics_0679
code .
```

## 3. Abrir terminal en VS Code
- `Ctrl + Ñ` o ir a `Terminal > New Terminal`

## 4. Crear entorno virtual
```bash
python -m venv .venv
```

## 5. Activar entorno virtual
**Windows:**
```bash
.venv\Scripts\activate
```
**Linux/Mac:**
```bash
source .venv/bin/activate
```

## 6. Activar intérprete de Python
- `Ctrl + Shift + P`
- Buscar "Python: Select Interpreter"
- Seleccionar `.venv\Scripts\python.exe`

## 7. Instalar Django
```bash
pip install django
```

## 8. Crear proyecto Django
```bash
django-admin startproject backend_Comics .
```

## 9. Ejecutar servidor en puerto 8079
```bash
python manage.py runserver 8079
```

## 10. Verificar en navegador
- Abrir navegador e ir a: `http://localhost:8079`

## 11. Crear aplicación app_Comics
```bash
python manage.py startapp app_Comics
```

## 12. Configurar models.py

**app_Comics/models.py:**
```python
from django.db import models

class Proveedor(models.Model):
    nombre_Empresa = models.CharField(max_length=100)
    nombre_Contacto = models.CharField(max_length=100)
    telefono_Contacto = models.CharField(max_length=15)
    email_Contacto = models.EmailField(unique=True)
    direccion = models.CharField(max_length=255)
    ciudad = models.CharField(max_length=100)
    pais = models.CharField(max_length=100)
    fecha_registro = models.DateField(auto_now_add=True)

    def __str__(self):
        return f"{self.nombre_Empresa} - {self.nombre_Contacto}"

class Producto(models.Model):
    nombre = models.CharField(max_length=150)
    autor = models.CharField(max_length=100)
    editorial = models.CharField(max_length=100)
    genero = models.CharField(max_length=50)
    precio = models.DecimalField(max_digits=10, decimal_places=2)
    stock = models.IntegerField()
    fecha_publicacion = models.DateField()
    isbn = models.CharField(max_length=13, unique=True)
    tipo_producto = models.IntegerField()
    proveedor = models.ForeignKey(Proveedor, on_delete=models.CASCADE, related_name='productos')

    def __str__(self):
        return f"{self.nombre} - {self.autor}"

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

## 12.5 Realizar migraciones
```bash
python manage.py makemigrations
python manage.py migrate
```

## 13. Configurar views.py

**app_Comics/views.py:**
```python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Producto, Proveedor, Cliente

def inicio_comics(request):
    return render(request, 'inicio.html')

# VISTAS PARA PRODUCTO
def agregar_Producto(request):
    if request.method == 'POST':
        nombre = request.POST['nombre']
        autor = request.POST['autor']
        editorial = request.POST['editorial']
        genero = request.POST['genero']
        precio = request.POST['precio']
        stock = request.POST['stock']
        fecha_publicacion = request.POST['fecha_publicacion']
        isbn = request.POST['isbn']
        tipo_producto = request.POST['tipo_producto']
        proveedor_id = request.POST['proveedor']
        
        proveedor = Proveedor.objects.get(id=proveedor_id)
        
        Producto.objects.create(
            nombre=nombre,
            autor=autor,
            editorial=editorial,
            genero=genero,
            precio=precio,
            stock=stock,
            fecha_publicacion=fecha_publicacion,
            isbn=isbn,
            tipo_producto=tipo_producto,
            proveedor=proveedor
        )
        return redirect('ver_Productos')
    
    proveedores = Proveedor.objects.all()
    return render(request, 'Producto/agregar_Productos.html', {'proveedores': proveedores})

def ver_Productos(request):
    productos = Producto.objects.all()
    return render(request, 'Producto/ver_Productos.html', {'productos': productos})

def actualizar_Producto(request, producto_id):
    producto = get_object_or_404(Producto, id=producto_id)
    proveedores = Proveedor.objects.all()
    return render(request, 'Producto/actualizar_Productos.html', {
        'producto': producto,
        'proveedores': proveedores
    })

def realizar_actualizacion_Producto(request, producto_id):
    if request.method == 'POST':
        producto = get_object_or_404(Producto, id=producto_id)
        
        producto.nombre = request.POST['nombre']
        producto.autor = request.POST['autor']
        producto.editorial = request.POST['editorial']
        producto.genero = request.POST['genero']
        producto.precio = request.POST['precio']
        producto.stock = request.POST['stock']
        producto.fecha_publicacion = request.POST['fecha_publicacion']
        producto.isbn = request.POST['isbn']
        producto.tipo_producto = request.POST['tipo_producto']
        producto.proveedor_id = request.POST['proveedor']
        
        producto.save()
        return redirect('ver_Productos')

def borrar_Producto(request, producto_id):
    producto = get_object_or_404(Producto, id=producto_id)
    return render(request, 'Producto/borrar_Productos.html', {'producto': producto})

def confirmar_borrar_Producto(request, producto_id):
    if request.method == 'POST':
        producto = get_object_or_404(Producto, id=producto_id)
        producto.delete()
        return redirect('ver_Productos')
```

## 15-22. Crear estructura de templates

**Estructura de carpetas:**
```
app_Comics/
├── templates/
│   ├── base.html
│   ├── header.html
│   ├── navbar.html
│   ├── footer.html
│   ├── inicio.html
│   └── Producto/
│       ├── agregar_Productos.html
│       ├── ver_Productos.html
│       ├── actualizar_Productos.html
│       └── borrar_Productos.html
```

**app_Comics/templates/base.html:**
```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Tienda de Cómics</title>
    <!-- Bootstrap CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet">
    <style>
        body {
            background-color: #f8f9fa;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        .navbar {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
        }
        .footer {
            background-color: #343a40;
            color: white;
            position: fixed;
            bottom: 0;
            width: 100%;
        }
        .card {
            border: none;
            border-radius: 15px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
            transition: transform 0.3s ease;
        }
        .card:hover {
            transform: translateY(-5px);
        }
        .btn-primary {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            border: none;
        }
    </style>
</head>
<body>
    {% include 'header.html' %}
    {% include 'navbar.html' %}
    
    <div class="container mt-4" style="padding-bottom: 100px;">
        {% block content %}
        {% endblock %}
    </div>
    
    {% include 'footer.html' %}
    
    <!-- Bootstrap JS -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

**app_Comics/templates/navbar.html:**
```html
<nav class="navbar navbar-expand-lg navbar-dark">
    <div class="container">
        <a class="navbar-brand" href="{% url 'inicio_comics' %}">
            <i class="fas fa-book"></i> Sistema de Administración Comics
        </a>
        
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav">
            <span class="navbar-toggler-icon"></span>
        </button>
        
        <div class="collapse navbar-collapse" id="navbarNav">
            <ul class="navbar-nav me-auto">
                <li class="nav-item">
                    <a class="nav-link" href="{% url 'inicio_comics' %}">
                        <i class="fas fa-home"></i> Inicio
                    </a>
                </li>
                
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" id="productosDropdown" role="button" data-bs-toggle="dropdown">
                        <i class="fas fa-box"></i> Productos
                    </a>
                    <ul class="dropdown-menu">
                        <li><a class="dropdown-item" href="{% url 'agregar_Producto' %}">Agregar Productos</a></li>
                        <li><a class="dropdown-item" href="{% url 'ver_Productos' %}">Ver Productos</a></li>
                        <li><hr class="dropdown-divider"></li>
                        <li><a class="dropdown-item" href="#">Actualizar Productos</a></li>
                        <li><a class="dropdown-item" href="#">Borrar Productos</a></li>
                    </ul>
                </li>
                
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" id="proveedoresDropdown" role="button" data-bs-toggle="dropdown">
                        <i class="fas fa-truck"></i> Proveedores
                    </a>
                    <ul class="dropdown-menu">
                        <li><a class="dropdown-item" href="#">Agregar Proveedores</a></li>
                        <li><a class="dropdown-item" href="#">Ver Proveedores</a></li>
                        <li><hr class="dropdown-divider"></li>
                        <li><a class="dropdown-item" href="#">Actualizar Proveedores</a></li>
                        <li><a class="dropdown-item" href="#">Borrar Proveedores</a></li>
                    </ul>
                </li>
                
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" id="clientesDropdown" role="button" data-bs-toggle="dropdown">
                        <i class="fas fa-users"></i> Clientes
                    </a>
                    <ul class="dropdown-menu">
                        <li><a class="dropdown-item" href="#">Agregar Clientes</a></li>
                        <li><a class="dropdown-item" href="#">Ver Clientes</a></li>
                        <li><hr class="dropdown-divider"></li>
                        <li><a class="dropdown-item" href="#">Actualizar Clientes</a></li>
                        <li><a class="dropdown-item" href="#">Borrar Clientes</a></li>
                    </ul>
                </li>
            </ul>
        </div>
    </div>
</nav>

<!-- Font Awesome para iconos -->
<script src="https://kit.fontawesome.com/your-fontawesome-kit.js" crossorigin="anonymous"></script>
```

**app_Comics/templates/footer.html:**
```html
<footer class="footer mt-5 py-3">
    <div class="container text-center">
        <span>&copy; {% now "Y" %} Tienda de Cómics - Todos los derechos reservados</span>
        <br>
        <small>{{ current_date|date:"d/m/Y" }} - Creado por Andrea Roldan 5I, Cbtis 128</small>
    </div>
</footer>
```

**app_Comics/templates/inicio.html:**
```html
{% extends 'base.html' %}

{% block content %}
<div class="row">
    <div class="col-md-6">
        <div class="card">
            <div class="card-body">
                <h2 class="card-title">Bienvenido al Sistema de Administración de Cómics</h2>
                <p class="card-text">
                    Gestiona tu tienda de cómics de manera eficiente con nuestro sistema. 
                    Controla productos, proveedores y clientes en un solo lugar.
                </p>
                <ul>
                    <li>Gestión completa de inventario</li>
                    <li>Control de proveedores</li>
                    <li>Administración de clientes</li>
                    <li>Reportes y estadísticas</li>
                </ul>
            </div>
        </div>
    </div>
    <div class="col-md-6">
        <div class="card">
            <div class="card-body text-center">
                <img src="https://images.unsplash.com/photo-1635805737707-575885ab0820?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=1000&q=80" 
                     alt="Cómics" class="img-fluid rounded" style="max-height: 300px;">
                <h4 class="mt-3">¡Explora el mundo de los cómics!</h4>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

**app_Comics/templates/Producto/agregar_Productos.html:**
```html
{% extends 'base.html' %}

{% block content %}
<div class="row justify-content-center">
    <div class="col-md-8">
        <div class="card">
            <div class="card-header bg-primary text-white">
                <h4 class="mb-0"><i class="fas fa-plus-circle"></i> Agregar Nuevo Producto</h4>
            </div>
            <div class="card-body">
                <form method="POST">
                    {% csrf_token %}
                    <div class="row">
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Nombre</label>
                            <input type="text" class="form-control" name="nombre" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Autor</label>
                            <input type="text" class="form-control" name="autor" required>
                        </div>
                    </div>
                    
                    <div class="row">
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Editorial</label>
                            <input type="text" class="form-control" name="editorial" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Género</label>
                            <input type="text" class="form-control" name="genero" required>
                        </div>
                    </div>
                    
                    <div class="row">
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Precio</label>
                            <input type="number" step="0.01" class="form-control" name="precio" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Stock</label>
                            <input type="number" class="form-control" name="stock" required>
                        </div>
                    </div>
                    
                    <div class="row">
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Fecha Publicación</label>
                            <input type="date" class="form-control" name="fecha_publicacion" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label class="form-label">ISBN</label>
                            <input type="text" class="form-control" name="isbn" required>
                        </div>
                    </div>
                    
                    <div class="row">
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Tipo Producto</label>
                            <input type="number" class="form-control" name="tipo_producto" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Proveedor</label>
                            <select class="form-select" name="proveedor" required>
                                <option value="">Seleccionar proveedor</option>
                                {% for proveedor in proveedores %}
                                <option value="{{ proveedor.id }}">{{ proveedor.nombre_Empresa }}</option>
                                {% endfor %}
                            </select>
                        </div>
                    </div>
                    
                    <div class="d-grid gap-2">
                        <button type="submit" class="btn btn-primary">Guardar Producto</button>
                        <a href="{% url 'ver_Productos' %}" class="btn btn-secondary">Cancelar</a>
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

**app_Comics/templates/Producto/ver_Productos.html:**
```html
{% extends 'base.html' %}

{% block content %}
<div class="card">
    <div class="card-header bg-primary text-white d-flex justify-content-between align-items-center">
        <h4 class="mb-0"><i class="fas fa-list"></i> Lista de Productos</h4>
        <a href="{% url 'agregar_Producto' %}" class="btn btn-light">
            <i class="fas fa-plus"></i> Nuevo Producto
        </a>
    </div>
    <div class="card-body">
        <div class="table-responsive">
            <table class="table table-striped table-hover">
                <thead class="table-dark">
                    <tr>
                        <th>ID</th>
                        <th>Nombre</th>
                        <th>Autor</th>
                        <th>Editorial</th>
                        <th>Precio</th>
                        <th>Stock</th>
                        <th>Acciones</th>
                    </tr>
                </thead>
                <tbody>
                    {% for producto in productos %}
                    <tr>
                        <td>{{ producto.id }}</td>
                        <td>{{ producto.nombre }}</td>
                        <td>{{ producto.autor }}</td>
                        <td>{{ producto.editorial }}</td>
                        <td>${{ producto.precio }}</td>
                        <td>{{ producto.stock }}</td>
                        <td>
                            <a href="#" class="btn btn-info btn-sm" title="Ver">
                                <i class="fas fa-eye"></i>
                            </a>
                            <a href="{% url 'actualizar_Producto' producto.id %}" class="btn btn-warning btn-sm" title="Editar">
                                <i class="fas fa-edit"></i>
                            </a>
                            <a href="{% url 'borrar_Producto' producto.id %}" class="btn btn-danger btn-sm" title="Borrar">
                                <i class="fas fa-trash"></i>
                            </a>
                        </td>
                    </tr>
                    {% empty %}
                    <tr>
                        <td colspan="7" class="text-center">No hay productos registrados</td>
                    </tr>
                    {% endfor %}
                </tbody>
            </table>
        </div>
    </div>
</div>
{% endblock %}
```

**app_Comics/templates/Producto/actualizar_Productos.html:**
```html
{% extends 'base.html' %}

{% block content %}
<div class="row justify-content-center">
    <div class="col-md-8">
        <div class="card">
            <div class="card-header bg-warning text-white">
                <h4 class="mb-0"><i class="fas fa-edit"></i> Actualizar Producto</h4>
            </div>
            <div class="card-body">
                <form method="POST" action="{% url 'realizar_actualizacion_Producto' producto.id %}">
                    {% csrf_token %}
                    <div class="row">
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Nombre</label>
                            <input type="text" class="form-control" name="nombre" value="{{ producto.nombre }}" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Autor</label>
                            <input type="text" class="form-control" name="autor" value="{{ producto.autor }}" required>
                        </div>
                    </div>
                    
                    <!-- Resto de campos similares a agregar_Productos.html -->
                    
                    <div class="d-grid gap-2">
                        <button type="submit" class="btn btn-warning">Actualizar Producto</button>
                        <a href="{% url 'ver_Productos' %}" class="btn btn-secondary">Cancelar</a>
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

**app_Comics/templates/Producto/borrar_Productos.html:**
```html
{% extends 'base.html' %}

{% block content %}
<div class="row justify-content-center">
    <div class="col-md-6">
        <div class="card">
            <div class="card-header bg-danger text-white">
                <h4 class="mb-0"><i class="fas fa-exclamation-triangle"></i> Confirmar Eliminación</h4>
            </div>
            <div class="card-body text-center">
                <h5>¿Estás seguro de que deseas eliminar este producto?</h5>
                <p class="text-muted">{{ producto.nombre }} - {{ producto.autor }}</p>
                
                <form method="POST" action="{% url 'confirmar_borrar_Producto' producto.id %}">
                    {% csrf_token %}
                    <div class="d-grid gap-2">
                        <button type="submit" class="btn btn-danger">Sí, Eliminar</button>
                        <a href="{% url 'ver_Productos' %}" class="btn btn-secondary">Cancelar</a>
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

## 24. Crear urls.py en app_Comics

**app_Comics/urls.py:**
```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_comics, name='inicio_comics'),
    
    # URLs para Producto
    path('productos/agregar/', views.agregar_Producto, name='agregar_Producto'),
    path('productos/', views.ver_Productos, name='ver_Productos'),
    path('productos/actualizar/<int:producto_id>/', views.actualizar_Producto, name='actualizar_Producto'),
    path('productos/realizar_actualizacion/<int:producto_id>/', views.realizar_actualizacion_Producto, name='realizar_actualizacion_Producto'),
    path('productos/borrar/<int:producto_id>/', views.borrar_Producto, name='borrar_Producto'),
    path('productos/confirmar_borrar/<int:producto_id>/', views.confirmar_borrar_Producto, name='confirmar_borrar_Producto'),
]
```

## 25. Agregar app_Comics en settings.py

**backend_Comics/settings.py:**
```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app_Comics',  # Agregar esta línea
]
```

## 26. Configurar urls.py principal

**backend_Comics/urls.py:**
```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Comics.urls')),  # Incluir URLs de la app
]
```

## 27. Registrar modelos en admin.py

**app_Comics/admin.py:**
```python
from django.contrib import admin
from .models import Producto, Proveedor, Cliente

@admin.register(Producto)
class ProductoAdmin(admin.ModelAdmin):
    list_display = ('nombre', 'autor', 'editorial', 'precio', 'stock')
    list_filter = ('genero', 'editorial')
    search_fields = ('nombre', 'autor')

@admin.register(Proveedor)
class ProveedorAdmin(admin.ModelAdmin):
    list_display = ('nombre_Empresa', 'nombre_Contacto', 'telefono_Contacto', 'ciudad')
    search_fields = ('nombre_Empresa', 'nombre_Contacto')

@admin.register(Cliente)
class ClienteAdmin(admin.ModelAdmin):
    list_display = ('nombre', 'email', 'telefono', 'ciudad')
    search_fields = ('nombre', 'email')

# Realizar migraciones nuevamente
# python manage.py makemigrations
# python manage.py migrate
```

## 31. Ejecutar servidor final
```bash
python manage.py runserver 8079
```

El proyecto estará disponible en: `http://localhost:8079`

**Estructura final del proyecto:**
```
UIII_Tienda_Comics_0679/
├── .venv/
├── backend_Comics/
│   ├── settings.py
│   ├── urls.py
│   └── ...
├── app_Comics/
│   ├── migrations/
│   ├── templates/
│   │   ├── base.html
│   │   ├── header.html
│   │   ├── navbar.html
│   │   ├── footer.html
│   │   ├── inicio.html
│   │   └── Producto/
│   │       ├── agregar_Productos.html
│   │       ├── ver_Productos.html
│   │       ├── actualizar_Productos.html
│   │       └── borrar_Productos.html
│   ├── admin.py
│   ├── models.py
│   ├── views.py
│   ├── urls.py
│   └── ...
├── manage.py
└── requirements.txt
```
