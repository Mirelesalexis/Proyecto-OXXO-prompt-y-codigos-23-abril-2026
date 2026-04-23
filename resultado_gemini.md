¡Hola! Entiendo perfectamente el desafío. Vamos a construir un ecosistema robusto llamado **cruddominos**. Como "Software Creator", no solo te daré el código, sino la estructura profesional necesaria para que tus estudiantes comprendan la integración entre **Flutter**, **Firebase** y la metodología de **Antigravity**.

---

## 1. Preparación del Entorno
Primero, aseguremos la jerarquía de archivos solicitada desde tu terminal:

```bash
mkdir cfluttermireles0644
cd cfluttermireles0644
flutter create cruddominos
cd cruddominos
```

---

## 2. Metodología Antigravity: Agentes y Roles
En un entorno de desarrollo moderno (y para fines académicos), definimos el flujo de trabajo mediante **Agentes**. Aquí está la configuración para el proyecto:

| Agente | Rol | Skill (Habilidad) |
| :--- | :--- | :--- |
| **Arquitecto Cloud** | Configuración Firebase | Consola Firebase, Reglas de Firestore. |
| **Ingeniero de Datos** | Modelado | Estructura de documentos (Nombre, Edad, Puesto). |
| **Dev Flutter** | Implementación UI/UX | Manejo de Widgets, validación de formularios. |
| **QA Specialist** | Pruebas CRUD | Verificación de persistencia en tiempo real. |

**Flujo de Trabajo:** 1. El Arquitecto vincula el proyecto.
2. El Ingeniero define el modelo en Dart.
3. El Dev crea las vistas (UI) y la lógica de Firebase.

---

## 3. Configuración de Firebase y Librerías
Para que el proyecto funcione, necesitas registrar tu app en la [Consola de Firebase](https://console.firebase.google.com/), descargar el archivo `google-services.json` y colocarlo en `android/app/`.

### Modificación de `pubspec.yaml`
Agregaremos las dependencias necesarias. Fíjate en la indentación, es crucial:

```yaml
dependencies:
  flutter:
    sdk: flutter
  # Librerías core para Firebase
  firebase_core: ^2.24.2
  cloud_firestore: ^4.14.0
  # Diseño atractivo
  google_fonts: ^6.1.0
```

---

## 4. Estructura de Archivos del Proyecto
Para mantener el orden de **Antigravity**, usaremos esta estructura:

```text
cruddominos/
├── lib/
│   ├── main.dart             # Punto de entrada
│   ├── models/
│   │   └── empleado.dart     # Estructura del dato
│   ├── services/
│   │   └── firebase_service.dart # Lógica CRUD (Agente de Datos)
│   └── pages/
│       ├── home_page.dart    # Lista de empleados (Lectura/Borrado)
│       └── add_empleado.dart # Formulario (Crear/Editar)
```

---

## 5. Implementación del Código (Totalmente Funcional)

### A. Modelo de Datos (`lib/models/empleado.dart`)
```dart
class Empleado {
  String id;
  String nombre;
  int edad;
  String puesto;

  Empleado({required this.id, required this.nombre, required this.edad, required this.puesto});
}
```

### B. Servicio CRUD (`lib/services/firebase_service.dart`)
Este es el corazón lógico de la aplicación.
```dart
import 'package:cloud_firestore/cloud_firestore.dart';

FirebaseFirestore db = FirebaseFirestore.instance;

// LEER
Future<List> getEmpleados() async {
  List empleados = [];
  QuerySnapshot querySnapshot = await db.collection('empleados').get();
  for (var doc in querySnapshot.docs) {
    final Map<String, dynamic> data = doc.data() as Map<String, dynamic>;
    final person = {
      "id": doc.id,
      "nombre": data['nombre'],
      "edad": data['edad'],
      "puesto": data['puesto'],
    };
    empleados.add(person);
  }
  return empleados;
}

// GUARDAR
Future<void> addEmpleado(String nombre, int edad, String puesto) async {
  await db.collection('empleados').add({
    "nombre": nombre,
    "edad": edad,
    "puesto": puesto,
  });
}

// ACTUALIZAR
Future<void> updateEmpleado(String id, String nombre, int edad, String puesto) async {
  await db.collection('empleados').doc(id).set({
    "nombre": nombre,
    "edad": edad,
    "puesto": puesto,
  });
}

// BORRAR
Future<void> deleteEmpleado(String id) async {
  await db.collection('empleados').doc(id).delete();
}
```

### C. Interfaz de Usuario: Home (`lib/pages/home_page.dart`)
Usaremos colores **Deep Orange** y **Amber** para un look atractivo (estilo "Dominos").

```dart
import 'package:flutter/material.dart';
import '../services/firebase_service.dart';

class Home extends StatefulWidget {
  @override
  _HomeState createState() => _HomeState();
}

class _HomeState extends State<Home> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('CRUD Dominos Empleados'),
        backgroundColor: Colors.deepOrange,
      ),
      body: FutureBuilder(
        future: getEmpleados(),
        builder: (context, snapshot) {
          if (snapshot.hasData) {
            return ListView.builder(
              itemCount: snapshot.data?.length,
              itemBuilder: (context, index) {
                return ListTile(
                  title: Text(snapshot.data?[index]['nombre'], 
                    style: TextStyle(fontWeight: FontWeight.bold, color: Colors.blue[900])),
                  subtitle: Text("${snapshot.data?[index]['puesto']} - ${snapshot.data?[index]['edad']} años"),
                  trailing: IconButton(
                    icon: Icon(Icons.delete, color: Colors.red),
                    onPressed: () async {
                      await deleteEmpleado(snapshot.data?[index]['id']);
                      setState(() {}); // Refrescar lista
                    },
                  ),
                  onTap: () async {
                    await Navigator.pushNamed(context, '/edit', arguments: snapshot.data?[index]);
                    setState(() {});
                  },
                );
              },
            );
          } else {
            return const Center(child: CircularProgressIndicator());
          }
        },
      ),
      floatingActionButton: FloatingActionButton(
        backgroundColor: Colors.deepOrange,
        onPressed: () async {
          await Navigator.pushNamed(context, '/add');
          setState(() {});
        },
        child: const Icon(Icons.add),
      ),
    );
  }
}
```

### D. Inicialización (`lib/main.dart`)
```dart
import 'package:flutter/material.dart';
import 'package:firebase_core/firebase_core.dart';
import 'pages/home_page.dart';
// Importa las demás páginas...

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp(); // Inicializa Firebase
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'CRUD Dominos',
      theme: ThemeData(primarySwatch: Colors.deepOrange),
      initialRoute: '/',
      routes: {
        '/': (context) => Home(),
        // Define aquí las rutas para añadir y editar
      },
    );
  }
}
```

---

## 6. Práctica Guiada (Estudiantes)
Para que los estudiantes dominen **Antigravity**, pídeles completar estos hitos:

1.  **Hito 1:** Crear el proyecto y conectar la consola de Firebase (Agente Arquitecto).
2.  **Hito 2:** Implementar la función `addEmpleado` y verificar que el dato aparezca en Firestore (Agente de Datos).
3.  **Hito 3:** Diseñar el `Card` de empleado con `Colors.blueAccent` y `Colors.redAccent` para diferenciar roles (Agente Dev).

> **Nota de Seguridad:** En la consola de Firebase, recuerda ir a la pestaña **Rules** de Firestore y cambiar `allow read, write: if false;` a `allow read, write: if true;` (solo para fines de práctica local, no para producción).
