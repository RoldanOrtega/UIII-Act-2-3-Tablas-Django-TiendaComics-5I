# Proyecto Tienda de Cómics - Django - PASO A PASO COMPLETO

## 1. Procedimiento para crear carpeta del Proyecto
```bash
mkdir UIII_Tienda_Comics_0679
```

## 2. Procedimiento para abrir VS Code sobre la carpeta
```bash
cd UIII_Tienda_Comics_0679
code .
```

## 3. Procedimiento para abrir terminal en VS Code
- Presionar `Ctrl + Ñ` (Windows/Linux) o `Cmd + Ñ` (Mac)
- O ir al menú: `Terminal > New Terminal`

## 4. Procedimiento para crear carpeta entorno virtual ".venv"
```bash
python -m venv .venv
```

## 5. Procedimiento para activar el entorno virtual
**Windows:**
```bash
.venv\Scripts\activate
```

**Linux/Mac:**
```bash
source .venv/bin/activate
```

## 6. Procedimiento para activar intérprete de Python
- Presionar `Ctrl + Shift + P`
- Escribir: "Python: Select Interpreter"
- Seleccionar: `.venv\Scripts\python.exe` (Windows) o `.venv/bin/python` (Linux/Mac)

## 7. Procedimiento para instalar Django
```bash
pip install django
```

## 8. Procedimiento para crear proyecto backend_Comics
```bash
django-admin startproject backend_Comics .
```

## 9. Procedimiento para ejecutar servidor en el puerto 8079
```bash
python manage.py runserver 8079
```

## 10. Procedimiento para copiar y pegar el link en el navegador
- Abrir navegador web
- Ir a: `http://localhost:8079`
- Verificar que aparece página de éxito de Django

## 11. Procedimiento para crear aplicación app_Comics
```bash
python manage.py startapp app_Comics
```

## 12. Configurar el modelo models.py

**app_Comics/models.py:**
```python
from django.db import models

# -----------------------
# MODELO PROVEEDOR
# -----------------------
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
    tipo_producto = models.IntegerField()
    proveedor = models.ForeignKey(Proveedor, on_delete=models.CASCADE, related_name='productos')

    def __str__(self):
        return f"{self.nombre} - {self.autor}"


# ========================================== 
# MODELO: CLIENTE 
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

## 12.5 Procedimiento para realizar las migraciones
```bash
python manage.py makemigrations
python manage.py migrate
```

## 13. Trabajar con el MODELO: PRODUCTO (Configurar views.py)

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

## 14. Crear la carpeta "templates" dentro de "app_Comics"
- En VS Code, hacer clic derecho en la carpeta `app_Comics`
- Seleccionar "New Folder"
- Nombrar la carpeta: `templates`

## 15. En la carpeta templates crear los archivos html
Dentro de la carpeta `templates` crear estos archivos:
- `base.html`
- `header.html` 
- `navbar.html`
- `footer.html`
- `inicio.html`

## 16. En el archivo base.html agregar bootstrap

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
    <!-- Font Awesome -->
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        body {
            background-color: #f8f9fa;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            padding-bottom: 100px;
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
            padding: 10px 0;
        }
        .card {
            border: none;
            border-radius: 15px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
            transition: transform 0.3s ease;
            margin-bottom: 20px;
        }
        .card:hover {
            transform: translateY(-5px);
        }
        .btn-primary {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            border: none;
        }
        .btn-primary:hover {
            background: linear-gradient(135deg, #764ba2 0%, #667eea 100%);
        }
    </style>
</head>
<body>
    {% include 'header.html' %}
    {% include 'navbar.html' %}
    
    <div class="container mt-4">
        {% block content %}
        {% endblock %}
    </div>
    
    {% include 'footer.html' %}
    
    <!-- Bootstrap JS -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

## 17. En el archivo navbar.html incluir las opciones

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
```

**app_Comics/templates/header.html:**
```html
<header>
    <!-- Header vacío por ahora, se puede agregar contenido después -->
</header>
```

## 18. En el archivo footer.html incluir derechos de autor

**app_Comics/templates/footer.html:**
```html
<footer class="footer">
    <div class="container text-center">
        <span>&copy; {% now "Y" %} Tienda de Cómics - Todos los derechos reservados</span>
        <br>
        <small>{% now "d/m/Y H:i" %} - Creado por Andrea Roldan 5I, Cbtis 128</small>
    </div>
</footer>
```

## 19. En el archivo inicio.html colocar información del sistema

**app_Comics/templates/inicio.html:**
```html
{% extends 'base.html' %}

{% block content %}
<div class="row">
    <div class="col-md-6">
        <div class="card">
            <div class="card-body">
                <h2 class="card-title text-primary">Bienvenido al Sistema de Administración de Cómics</h2>
                <p class="card-text lead">
                    Gestiona tu tienda de cómics de manera eficiente con nuestro sistema. 
                    Controla productos, proveedores y clientes en un solo lugar.
                </p>
                <hr>
                <h5>Características principales:</h5>
                <ul class="list-group list-group-flush">
                    <li class="list-group-item"><i class="fas fa-check text-success"></i> Gestión completa de inventario</li>
                    <li class="list-group-item"><i class="fas fa-check text-success"></i> Control de proveedores</li>
                    <li class="list-group-item"><i class="fas fa-check text-success"></i> Administración de clientes</li>
                    <li class="list-group-item"><i class="fas fa-check text-success"></i> Reportes y estadísticas</li>
                    <li class="list-group-item"><i class="fas fa-check text-success"></i> Interfaz moderna y responsive</li>
                </ul>
            </div>
        </div>
    </div>
    <div class="col-md-6">
        <div class="card">
            <div class="card-body text-center">
                <img src="https://images.unsplash.com/photo-1635805737707-575885ab0820?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=1000&q=80" 
                     alt="Cómics" class="img-fluid rounded" style="max-height: 400px; width: 100%; object-fit: cover;">
                <h4 class="mt-3 text-info">¡Explora el mundo de los cómics!</h4>
                <p class="text-muted">Gestiona tu colección de manera profesional</p>
            </div>
        </div>
    </div>
</div>

<div class="row mt-4">
    <div class="col-12">
        <div class="card">
            <div class="card-body text-center">
                <h4 class="card-title">¿Listo para comenzar?</h4>
                <p class="card-text">Selecciona una opción del menú superior para empezar a gestionar tu tienda</p>
                <a href="{% url 'agregar_Producto' %}" class="btn btn-primary me-2">
                    <i class="fas fa-plus-circle"></i> Agregar Primer Producto
                </a>
                <a href="{% url 'ver_Productos' %}" class="btn btn-outline-primary">
                    <i class="fas fa-list"></i> Ver Productos Existentes
                </a>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

## 20. Crear la subcarpeta Producto dentro de app_Comics\templates
- Hacer clic derecho en la carpeta `templates`
- Seleccionar "New Folder"
- Nombrar la carpeta: `Producto`

## 21. Crear los archivos html en Producto

**app_Comics/templates/Producto/agregar_Productos.html:**
```html
{% extends 'base.html' %}

{% block content %}
<div class="row justify-content-center">
    <div class="col-md-10">
        <div class="card">
            <div class="card-header bg-primary text-white">
                <h4 class="mb-0"><i class="fas fa-plus-circle"></i> Agregar Nuevo Producto</h4>
            </div>
            <div class="card-body">
                <form method="POST">
                    {% csrf_token %}
                    <div class="row">
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Nombre del Cómic</label>
                            <input type="text" class="form-control" name="nombre" required placeholder="Ingresa el nombre del cómic">
                        </div>
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Autor</label>
                            <input type="text" class="form-control" name="autor" required placeholder="Nombre del autor">
                        </div>
                    </div>
                    
                    <div class="row">
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Editorial</label>
                            <input type="text" class="form-control" name="editorial" required placeholder="Ej: Marvel, DC, etc.">
                        </div>
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Género</label>
                            <input type="text" class="form-control" name="genero" required placeholder="Ej: Superhéroes, Manga, etc.">
                        </div>
                    </div>
                    
                    <div class="row">
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Precio ($)</label>
                            <input type="number" step="0.01" class="form-control" name="precio" required placeholder="0.00">
                        </div>
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Stock</label>
                            <input type="number" class="form-control" name="stock" required placeholder="Cantidad disponible">
                        </div>
                    </div>
                    
                    <div class="row">
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Fecha de Publicación</label>
                            <input type="date" class="form-control" name="fecha_publicacion" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label class="form-label">ISBN</label>
                            <input type="text" class="form-control" name="isbn" required placeholder="13 dígitos ISBN">
                        </div>
                    </div>
                    
                    <div class="row">
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Tipo de Producto</label>
                            <select class="form-select" name="tipo_producto" required>
                                <option value="">Seleccionar tipo</option>
                                <option value="1">Cómic físico</option>
                                <option value="2">Cómic digital</option>
                                <option value="3">Novela gráfica</option>
                                <option value="4">Manga</option>
                            </select>
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
                    
                    <div class="d-grid gap-2 d-md-flex justify-content-md-end">
                        <button type="submit" class="btn btn-primary me-md-2">
                            <i class="fas fa-save"></i> Guardar Producto
                        </button>
                        <a href="{% url 'ver_Productos' %}" class="btn btn-secondary">
                            <i class="fas fa-times"></i> Cancelar
                        </a>
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
        {% if productos %}
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
                        <th>Género</th>
                        <th>Acciones</th>
                    </tr>
                </thead>
                <tbody>
                    {% for producto in productos %}
                    <tr>
                        <td>{{ producto.id }}</td>
                        <td><strong>{{ producto.nombre }}</strong></td>
                        <td>{{ producto.autor }}</td>
                        <td>{{ producto.editorial }}</td>
                        <td>${{ producto.precio }}</td>
                        <td>
                            <span class="badge {% if producto.stock > 10 %}bg-success{% else %}bg-warning{% endif %}">
                                {{ producto.stock }}
                            </span>
                        </td>
                        <td>{{ producto.genero }}</td>
                        <td>
                            <div class="btn-group" role="group">
                                <a href="#" class="btn btn-info btn-sm" title="Ver detalles">
                                    <i class="fas fa-eye"></i>
                                </a>
                                <a href="{% url 'actualizar_Producto' producto.id %}" class="btn btn-warning btn-sm" title="Editar">
                                    <i class="fas fa-edit"></i>
                                </a>
                                <a href="{% url 'borrar_Producto' producto.id %}" class="btn btn-danger btn-sm" title="Eliminar">
                                    <i class="fas fa-trash"></i>
                                </a>
                            </div>
                        </td>
                    </tr>
                    {% endfor %}
                </tbody>
            </table>
        </div>
        {% else %}
        <div class="text-center py-4">
            <i class="fas fa-inbox fa-3x text-muted mb-3"></i>
            <h4 class="text-muted">No hay productos registrados</h4>
            <p class="text-muted">Comienza agregando tu primer producto</p>
            <a href="{% url 'agregar_Producto' %}" class="btn btn-primary">
                <i class="fas fa-plus"></i> Agregar Primer Producto
            </a>
        </div>
        {% endif %}
    </div>
</div>
{% endblock %}
```

**app_Comics/templates/Producto/actualizar_Productos.html:**
```html
{% extends 'base.html' %}

{% block content %}
<div class="row justify-content-center">
    <div class="col-md-10">
        <div class="card">
            <div class="card-header bg-warning text-white">
                <h4 class="mb-0"><i class="fas fa-edit"></i> Actualizar Producto: {{ producto.nombre }}</h4>
            </div>
            <div class="card-body">
                <form method="POST" action="{% url 'realizar_actualizacion_Producto' producto.id %}">
                    {% csrf_token %}
                    <div class="row">
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Nombre del Cómic</label>
                            <input type="text" class="form-control" name="nombre" value="{{ producto.nombre }}" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Autor</label>
                            <input type="text" class="form-control" name="autor" value="{{ producto.autor }}" required>
                        </div>
                    </div>
                    
                    <div class="row">
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Editorial</label>
                            <input type="text" class="form-control" name="editorial" value="{{ producto.editorial }}" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Género</label>
                            <input type="text" class="form-control" name="genero" value="{{ producto.genero }}" required>
                        </div>
                    </div>
                    
                    <div class="row">
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Precio ($)</label>
                            <input type="number" step="0.01" class="form-control" name="precio" value="{{ producto.precio }}" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Stock</label>
                            <input type="number" class="form-control" name="stock" value="{{ producto.stock }}" required>
                        </div>
                    </div>
                    
                    <div class="row">
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Fecha Publicación</label>
                            <input type="date" class="form-control" name="fecha_publicacion" value="{{ producto.fecha_publicacion|date:'Y-m-d' }}" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label class="form-label">ISBN</label>
                            <input type="text" class="form-control" name="isbn" value="{{ producto.isbn }}" required>
                        </div>
                    </div>
                    
                    <div class="row">
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Tipo Producto</label>
                            <select class="form-select" name="tipo_producto" required>
                                <option value="1" {% if producto.tipo_producto == 1 %}selected{% endif %}>Cómic físico</option>
                                <option value="2" {% if producto.tipo_producto == 2 %}selected{% endif %}>Cómic digital</option>
                                <option value="3" {% if producto.tipo_producto == 3 %}selected{% endif %}>Novela gráfica</option>
                                <option value="4" {% if producto.tipo_producto == 4 %}selected{% endif %}>Manga</option>
                            </select>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Proveedor</label>
                            <select class="form-select" name="proveedor" required>
                                {% for proveedor in proveedores %}
                                <option value="{{ proveedor.id }}" {% if producto.proveedor.id == proveedor.id %}selected{% endif %}>
                                    {{ proveedor.nombre_Empresa }}
                                </option>
                                {% endfor %}
                            </select>
                        </div>
                    </div>
                    
                    <div class="d-grid gap-2 d-md-flex justify-content-md-end">
                        <button type="submit" class="btn btn-warning me-md-2">
                            <i class="fas fa-sync-alt"></i> Actualizar Producto
                        </button>
                        <a href="{% url 'ver_Productos' %}" class="btn btn-secondary">
                            <i class="fas fa-times"></i> Cancelar
                        </a>
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
                <div class="alert alert-warning" role="alert">
                    <h5><i class="fas fa-exclamation-circle"></i> ¡Atención!</h5>
                    <p class="mb-0">¿Estás seguro de que deseas eliminar este producto?</p>
                </div>
                
                <div class="card mb-3">
                    <div class="card-body">
                        <h5 class="card-title">{{ producto.nombre }}</h5>
                        <p class="card-text">
                            <strong>Autor:</strong> {{ producto.autor }}<br>
                            <strong>Editorial:</strong> {{ producto.editorial }}<br>
                            <strong>Precio:</strong> ${{ producto.precio }}<br>
                            <strong>Stock:</strong> {{ producto.stock }}
                        </p>
                    </div>
                </div>
                
                <p class="text-muted"><small>Esta acción no se puede deshacer.</small></p>
                
                <form method="POST" action="{% url 'confirmar_borrar_Producto' producto.id %}">
                    {% csrf_token %}
                    <div class="d-grid gap-2">
                        <button type="submit" class="btn btn-danger btn-lg">
                            <i class="fas fa-trash"></i> Sí, Eliminar Producto
                        </button>
                        <a href="{% url 'ver_Productos' %}" class="btn btn-secondary">
                            <i class="fas fa-times"></i> Cancelar
                        </a>
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

## 22. No utilizar forms.py (Ya estamos usando formularios HTML directamente)

## 23. Procedimiento para crear el archivo urls.py en app_Comics

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

## 24. Procedimiento para agregar app_Comics en settings.py

**backend_Comics/settings.py:**
```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app_Comics',  # AGREGAR ESTA LÍNEA
]
```

## 25. Realizar configuraciones en urls.py de backend_Comics

**backend_Comics/urls.py:**
```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Comics.urls')),  # INCLUIR URLs DE LA APP
]
```

## 26. Procedimiento para registrar los modelos en admin.py

**app_Comics/admin.py:**
```python
from django.contrib import admin
from .models import Producto, Proveedor, Cliente

@admin.register(Proveedor)
class ProveedorAdmin(admin.ModelAdmin):
    list_display = ('nombre_Empresa', 'nombre_Contacto', 'telefono_Contacto', 'email_Contacto', 'ciudad')
    list_filter = ('ciudad', 'pais')
    search_fields = ('nombre_Empresa', 'nombre_Contacto')

@admin.register(Producto)
class ProductoAdmin(admin.ModelAdmin):
    list_display = ('nombre', 'autor', 'editorial', 'precio', 'stock', 'proveedor')
    list_filter = ('genero', 'editorial', 'tipo_producto')
    search_fields = ('nombre', 'autor', 'isbn')

@admin.register(Cliente)
class ClienteAdmin(admin.ModelAdmin):
    list_display = ('nombre', 'email', 'telefono', 'ciudad', 'fecha_registro')
    list_filter = ('ciudad', 'pais')
    search_fields = ('nombre', 'email')
```

## 27. Realizar migraciones nuevamente
```bash
python manage.py makemigrations
python manage.py migrate
```

## 28. Utilizar colores suaves, atractivos y modernos (Ya implementado en los templates)

## 29. No validar entrada de datos (Ya que no estamos usando Django Forms)

## 30. Al inicio crear la estructura completa de carpetas y archivos (Ya completado)

## 31. Proyecto totalmente funcional - Ejecutar servidor final
```bash
python manage.py runserver 8079
```

## Estructura final completa del proyecto:
```
UIII_Tienda_Comics_0679/
├── .venv/
├── backend_Comics/
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── app_Comics/
│   ├── migrations/
│   │   └── __init__.py
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
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── models.py
│   ├── tests.py
│   ├── urls.py
│   └── views.py
├── manage.py
└── db.sqlite3
```

**¡El proyecto estará funcionando en: `http://localhost:8079`!**
