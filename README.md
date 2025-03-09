# Documentación de la Base de Datos para una Fábrica de Ropa

## **1. Introducción**
Esta documentación describe el diseño de una base de datos para la gestión de inventarios, ventas, producción y administración financiera en una fábrica de ropa. El objetivo es optimizar el flujo de trabajo mediante una estructura clara y eficiente.

## **2. Objetivos del Sistema**
- **Control de Inventarios**: Gestionar productos, variantes y almacenes.
- **Gestión de Producción**: Registrar materiales, fabricación y movimientos de stock.
- **Manejo de Ventas y Pedidos**: Control de órdenes de compra y venta.
- **Administración Financiera**: Registrar pagos, cuentas por cobrar y reportes.
- **Gestión de Usuarios y Roles**: Definir accesos y permisos en el sistema.

---

## **3. Estructura de la Base de Datos**

### **3.1 Entidades Principales**

### **Producto**
Almacena información de las prendas fabricadas.
```sql
Producto (
  id INT PRIMARY KEY AUTO_INCREMENT,
  nombre VARCHAR(255) NOT NULL,
  descripcion TEXT,
  precioCosto DECIMAL(10,2) NOT NULL,
  precioVenta DECIMAL(10,2) NOT NULL,
  imageUrl VARCHAR(500),
  subcategoria_id INT,
  FOREIGN KEY (subcategoria_id) REFERENCES Subcategoria(id)
);
```

### **Variante**
Cada producto tiene variantes según color, talla y stock.
```sql
Variante (
  id INT PRIMARY KEY AUTO_INCREMENT,
  producto_id INT,
  color VARCHAR(50) NOT NULL,
  talla_id INT,
  sku VARCHAR(100) UNIQUE NOT NULL,
  stock INT NOT NULL DEFAULT 0,
  FOREIGN KEY (producto_id) REFERENCES Producto(id),
  FOREIGN KEY (talla_id) REFERENCES Talla(id)
);
```

### **VarianteImagen**
Asocia imágenes a cada variante de producto.
```sql
VarianteImagen (
  id INT PRIMARY KEY AUTO_INCREMENT,
  variante_id INT,
  imageUrl VARCHAR(500),
  FOREIGN KEY (variante_id) REFERENCES Variante(id)
);
```

### **Material**
Almacena los materiales usados en la fabricación.
```sql
Material (
  id INT PRIMARY KEY AUTO_INCREMENT,
  nombre VARCHAR(100) NOT NULL,
  descripcion TEXT
);
```

### **ProductoMaterial**
Relaciona productos con materiales usados en su fabricación.
```sql
ProductoMaterial (
  producto_id INT,
  material_id INT,
  PRIMARY KEY (producto_id, material_id),
  FOREIGN KEY (producto_id) REFERENCES Producto(id),
  FOREIGN KEY (material_id) REFERENCES Material(id)
);
```

### **Almacenes e Inventarios**
```sql
Almacen (
  id INT PRIMARY KEY AUTO_INCREMENT,
  nombre VARCHAR(255) NOT NULL,
  direccion TEXT NOT NULL,
  distrito VARCHAR(100) NOT NULL
);
```

```sql
AlmacenInventario (
  id INT PRIMARY KEY AUTO_INCREMENT,
  almacen_id INT,
  variante_id INT,
  stock INT NOT NULL DEFAULT 0,
  FOREIGN KEY (almacen_id) REFERENCES Almacen(id),
  FOREIGN KEY (variante_id) REFERENCES Variante(id)
);
```

### **Movimientos de Inventario**
```sql
InventarioMovimiento (
  id INT PRIMARY KEY AUTO_INCREMENT,
  variante_id INT NOT NULL,
  almacen_origen_id INT NULL,
  almacen_destino_id INT NULL,
  tipoMovimiento ENUM('entrada', 'salida', 'transferencia') NOT NULL,
  cantidad INT NOT NULL,
  fecha DATETIME DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (variante_id) REFERENCES Variante(id),
  FOREIGN KEY (almacen_origen_id) REFERENCES Almacen(id),
  FOREIGN KEY (almacen_destino_id) REFERENCES Almacen(id)
);
```

### **Órdenes y Detalles**
```sql
Orden (
  id INT PRIMARY KEY AUTO_INCREMENT,
  empresa_id INT,
  fecha_orden DATETIME DEFAULT CURRENT_TIMESTAMP,
  tipoOrden ENUM('venta', 'compra') NOT NULL,
  pagado BOOLEAN DEFAULT FALSE,
  anulado BOOLEAN DEFAULT FALSE,
  montoTotal DECIMAL(10,2),
  FOREIGN KEY (empresa_id) REFERENCES Empresa(id)
);
```

```sql
DetalleOrden (
  id INT PRIMARY KEY AUTO_INCREMENT,
  orden_id INT,
  variante_id INT,
  precio DECIMAL(10,2),
  cantidad INT NOT NULL,
  montoTotal DECIMAL(10,2),
  FOREIGN KEY (orden_id) REFERENCES Orden(id),
  FOREIGN KEY (variante_id) REFERENCES Variante(id)
);
```

### **Pagos y Finanzas**
```sql
Pago (
  id INT PRIMARY KEY AUTO_INCREMENT,
  orden_id INT,
  numOperacion VARCHAR(100) NOT NULL,
  fechaPago DATETIME DEFAULT CURRENT_TIMESTAMP,
  tipoPago ENUM('efectivo', 'tarjeta', 'transferencia') NOT NULL,
  montoCobrado DECIMAL(10,2),
  montoRecibido DECIMAL(10,2),
  vuelto DECIMAL(10,2),
  FOREIGN KEY (orden_id) REFERENCES Orden(id)
);
```

```sql
Cuenta (
  id INT PRIMARY KEY AUTO_INCREMENT,
  empresa_id INT,
  orden_id INT,
  tipoCuenta ENUM('por cobrar', 'por pagar') NOT NULL,
  estadoPago ENUM('pendiente', 'pagado', 'vencido') NOT NULL,
  total DECIMAL(10,2),
  balance DECIMAL(10,2),
  fechaVencimiento DATE,
  FOREIGN KEY (empresa_id) REFERENCES Empresa(id),
  FOREIGN KEY (orden_id) REFERENCES Orden(id)
);
```

### **Usuarios y Roles**
```sql
Usuario (
  id INT PRIMARY KEY AUTO_INCREMENT,
  email VARCHAR(255) UNIQUE NOT NULL,
  password VARCHAR(255) NOT NULL,
  nombre VARCHAR(100) NOT NULL,
  apellido VARCHAR(100) NOT NULL,
  rol_id INT,
  FOREIGN KEY (rol_id) REFERENCES Rol(id)
);
```

```sql
Rol (
  id INT PRIMARY KEY AUTO_INCREMENT,
  nombre VARCHAR(100) NOT NULL,
  descripcion TEXT
);
```

---

## **4. Flujo del Sistema**
1. **Producción y Almacenamiento**: Se crean productos y variantes, y se registran materiales utilizados.
2. **Control de Inventario**: Se asigna stock a cada almacén y se registran movimientos.
3. **Gestión de Ventas y Pedidos**: Se crean órdenes, se verifica stock y se procesan pagos.
4. **Administración Financiera**: Se registran pagos, cuentas por cobrar y balances.
5. **Gestión de Usuarios**: Se asignan roles y permisos para cada área de la fábrica.

---

## **5. Conclusión**
Esta base de datos permite optimizar la producción, controlar inventarios, gestionar ventas y administrar la contabilidad de una fábrica de ropa de manera eficiente.

**¿Se necesita algún ajuste adicional? 🚀**



