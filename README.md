# Control de Finanzas
Sistema web de control de entradas y salidas financieras.
**Stack:** Java 17 + Jakarta EE 10 + Payara 7.2026.2 + MySQL + JSP/CSS

---

## Requisitos previos
- JDK 17
- Maven 3.8+
- MySQL 8.x
- Payara Server 7.2026.2

---

## 1. Configurar la base de datos

Abre MySQL y ejecuta el script:

```sql
SOURCE /ruta/al/proyecto/sql/schema.sql;
```

Esto crea la base `control_finanzas` con las tablas `usuarios`, `entradas` y `salidas`, e inserta el usuario de prueba:

| Campo    | Valor    |
|----------|----------|
| Usuario  | admin    |
| Contrasena | admin123 |

---

## 2. Configurar la conexion a la BD

Edita el archivo:
`src/main/java/com/finanzas/util/ConexionDB.java`

Modifica estos valores segun tu instalacion:
```java
private static final String URL = "jdbc:mysql://localhost:3306/control_finanzas?...";
private static final String USER = "root";
private static final String PASSWORD = "tu_contrasena";
```

---

## 3. Compilar y empaquetar

Desde la raiz del proyecto ejecuta:

```bash
mvn clean package
```

Esto genera: `target/control-finanzas.war`

---

## 4. Desplegar en Payara 7

### Opcion A: Consola de administracion
1. Inicia Payara: `payara7/bin/asadmin start-domain`
2. Abre http://localhost:4848
3. Ve a **Applications > Deploy**
4. Selecciona el archivo `control-finanzas.war`
5. Haz clic en **OK**

### Opcion B: Linea de comandos
```bash
payara7/bin/asadmin deploy target/control-finanzas.war
```

---

## 5. Acceder al sistema

Abre el navegador en:
```
http://localhost:8080/control-finanzas
```

---

## Estructura del proyecto

```
control-finanzas/
├── sql/
│   └── schema.sql                    # Script de base de datos
├── src/main/
│   ├── java/com/finanzas/
│   │   ├── modelo/
│   │   │   ├── Usuario.java          # Clase usuario
│   │   │   ├── Entrada.java          # Clase entrada
│   │   │   ├── Salida.java           # Clase salida
│   │   │   └── ReporteBalance.java   # Clase reporte de balance
│   │   ├── dao/
│   │   │   ├── LoginDAO.java         # Acceso a datos de login
│   │   │   ├── EntradaDAO.java       # Acceso a datos de entradas
│   │   │   └── SalidaDAO.java        # Acceso a datos de salidas
│   │   ├── servlet/
│   │   │   ├── LoginServlet.java     # Controlador login
│   │   │   ├── DashboardServlet.java # Controlador dashboard
│   │   │   ├── EntradaServlet.java   # Controlador entradas
│   │   │   ├── SalidaServlet.java    # Controlador salidas
│   │   │   ├── BalanceServlet.java   # Controlador balance + PDF
│   │   │   └── LogoutServlet.java    # Controlador logout
│   │   └── util/
│   │       └── ConexionDB.java       # Conexion MySQL
│   └── webapp/
│       ├── css/
│       │   └── style.css             # Estilos (Poppins, gris/verde)
│       ├── views/
│       │   ├── login.jsp
│       │   ├── menu.jsp              # Sidebar (include)
│       │   ├── dashboard.jsp
│       │   ├── registrar_entrada.jsp
│       │   ├── registrar_salida.jsp
│       │   ├── ver_entradas.jsp
│       │   ├── ver_salidas.jsp
│       │   └── balance.jsp           # Reporte con Chart.js
│       ├── uploads/                  # Carpeta de facturas subidas
│       ├── index.jsp
│       └── WEB-INF/
│           └── web.xml
└── pom.xml
```

---

## Funcionalidades

| Funcionalidad                | Descripcion                                         |
|------------------------------|-----------------------------------------------------|
| Login con sesion             | Autenticacion con SHA-256 en MySQL                  |
| Dashboard                    | Resumen de totales y balance rapido                 |
| Registrar entrada            | Tipo, monto, fecha, foto de factura                 |
| Registrar salida             | Tipo, monto, fecha, foto de factura                 |
| Ver entradas                 | Tabla con imagen clickeable para ver factura grande |
| Ver salidas                  | Tabla con imagen clickeable para ver factura grande |
| Reporte de balance           | Tabla doble + balance resultante + grafico de pastel|
| Exportar PDF                 | Genera PDF con tablas, balance y grafico de pastel  |
| Logout                       | Invalida la sesion                                  |

---

## Notas de seguridad
- Las contrasenas se almacenan con SHA-256 en MySQL (funcion `SHA2`)
- Las sesiones expiran automaticamente en 60 minutos
- Las rutas de facturas se guardan en la BD; los archivos en `/uploads/`
- Se validan todos los campos requeridos en servidor
