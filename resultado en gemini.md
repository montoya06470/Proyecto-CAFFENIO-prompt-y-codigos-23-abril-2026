¡Excelente elección! Vamos a construir este sistema de gestión para "Caffenio" utilizando una arquitectura robusta. Para esta práctica, seguiremos la metodología **Antigravity**, que se centra en la orquestación de **Agentes, Roles y Skills** para desacoplar la lógica de la interfaz.

---

## 1. Configuración Inicial y Estructura

Primero, situémonos en tu entorno de desarrollo. Ejecuta estos comandos en tu terminal para inicializar el proyecto:

```bash
# Crear directorio raíz y proyecto Flutter
mkdir xfluttermontoya0647
cd xfluttermontoya0647
flutter create crudcaffenio
cd crudcaffenio
```

### Estructura de Carpetas (Arquitectura Antigravity)
Organizaremos el código para que los "Agentes" (lógica) estén separados de los "Skins" (UI).

```text
crudcaffenio/
├── lib/
│   ├── agents/          # Orquestadores de flujo
│   │   └── cafe_agent.dart
│   ├── roles/           # Definición de responsabilidades (Interfaces)
│   │   └── database_role.dart
│   ├── skills/          # Implementaciones técnicas (Firebase)
│   │   └── firestore_skill.dart
│   ├── models/          # Entidades de datos
│   │   └── cafe_model.dart
│   ├── ui/              # Widgets y diseño elegante
│   │   ├── theme.dart
│   │   └── home_page.dart
│   └── main.dart
└── pubspec.yaml
```

---

## 2. Dependencias y Base de Datos

### Configuración de `pubspec.yaml`
Agrega las librerías necesarias bajo la sección `dependencies`:

```yaml
dependencies:
  flutter:
    sdk: flutter
  firebase_core: ^3.0.0
  cloud_firestore: ^5.0.0
  google_fonts: ^6.0.0  # Para un diseño más profesional
```
*Nota: No olvides ejecutar `flutter pub get` después de guardar.*

### Base de Datos en Firestore
1. Ve a [Firebase Console](https://console.firebase.google.com/).
2. Crea un proyecto nuevo y añade una app de Android/iOS.
3. En **Firestore Database**, crea una colección llamada `cafes`.
4. Los documentos deben tener esta estructura:
   * `nombre`: String
   * `precio`: Number
   * `stock`: Number

---

## 3. Metodología Antigravity: Paso a Paso

En esta práctica guiada, entenderemos cómo los componentes colaboran:

1.  **Rol (`DatabaseRole`):** Define *qué* necesitamos hacer (CRUD).
2.  **Skill (`FirestoreSkill`):** Es el experto que sabe *cómo* hablar con Firebase.
3.  **Agente (`CafeAgent`):** Es el director de orquesta que recibe la orden de la UI y usa la Skill.

### El Modelo de Datos (`lib/models/cafe_model.dart`)
```dart
class Cafe {
  String? id;
  String nombre;
  double precio;
  int stock;

  Cafe({this.id, required this.nombre, required this.precio, required this.stock});

  Map<String, dynamic> toMap() => {
    'nombre': nombre,
    'precio': precio,
    'stock': stock,
  };

  factory Cafe.fromMap(Map<String, dynamic> map, String id) {
    return Cafe(
      id: id,
      nombre: map['nombre'] ?? '',
      precio: (map['precio'] ?? 0).toDouble(),
      stock: map['stock'] ?? 0,
    );
  }
}
```

---

## 4. Implementación de Agentes y Skills

### La Skill (`lib/skills/firestore_skill.dart`)
```dart
import 'package:cloud_firestore/cloud_firestore.dart';
import '../models/cafe_model.dart';

class FirestoreSkill {
  final FirebaseFirestore _db = FirebaseFirestore.instance;

  Stream<List<Cafe>> getCafes() {
    return _db.collection('cafes').snapshots().map((snapshot) =>
        snapshot.docs.map((doc) => Cafe.fromMap(doc.data(), doc.id)).toList());
  }

  Future<void> addCafe(Cafe cafe) => _db.collection('cafes').add(cafe.toMap());

  Future<void> updateCafe(Cafe cafe) => 
      _db.collection('cafes').doc(cafe.id).update(cafe.toMap());

  Future<void> deleteCafe(String id) => _db.collection('cafes').doc(id).delete();
}
```

---

## 5. UI: Diseño Elegante "Estilo Caffe"

Utilizaremos una paleta de colores café (Marrón profundo, crema y dorado).

### Tema Personalizado (`lib/ui/theme.dart`)
```dart
import 'package:flutter/material.dart';

class CaffeTheme {
  static const Color primaryCoffee = Color(0xFF4E342E);
  static const Color secondaryGold = Color(0xFFD4A373);
  static const Color backgroundCream = Color(0xFFF5F5DC);

  static ThemeData get theme => ThemeData(
    primaryColor: primaryCoffee,
    scaffoldBackgroundColor: backgroundCream,
    appBarTheme: const AppBarTheme(backgroundColor: primaryCoffee),
    floatingActionButtonTheme: const FloatingActionButtonThemeData(
      backgroundColor: secondaryGold,
    ),
  );
}
```

### Pantalla Principal CRUD (`lib/ui/home_page.dart`)
Este archivo integra el flujo completo.

```dart
import 'package:flutter/material.dart';
import '../skills/firestore_skill.dart';
import '../models/cafe_model.dart';
import 'theme.dart';

class HomePage extends StatelessWidget {
  final FirestoreSkill _skill = FirestoreSkill();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Caffenio Admin - Cafes')),
      body: StreamBuilder<List<Cafe>>(
        stream: _skill.getCafes(),
        builder: (context, snapshot) {
          if (!snapshot.hasData) return const Center(child: CircularProgressIndicator());
          final cafes = snapshot.data!;
          return ListView.builder(
            itemCount: cafes.length,
            itemBuilder: (context, index) => _cafeCard(context, cafes[index]),
          );
        },
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () => _showForm(context),
        child: const Icon(Icons.add),
      ),
    );
  }

  Widget _cafeCard(BuildContext context, Cafe cafe) {
    return Card(
      margin: const EdgeInsets.symmetric(horizontal: 15, vertical: 8),
      elevation: 4,
      child: ListTile(
        leading: const Icon(Icons.coffee, color: CaffeTheme.primaryCoffee, size: 40),
        title: Text(cafe.nombre, style: const TextStyle(fontWeight: FontWeight.bold)),
        subtitle: Text('Precio: \$${cafe.precio} | Stock: ${cafe.stock}'),
        trailing: Row(
          mainAxisSize: MainAxisSize.min,
          children: [
            IconButton(icon: const Icon(Icons.edit, color: Colors.blue), onPressed: () => _showForm(context, cafe)),
            IconButton(icon: const Icon(Icons.delete, color: Colors.red), onPressed: () => _skill.deleteCafe(cafe.id!)),
          ],
        ),
      ),
    );
  }

  void _showForm(BuildContext context, [Cafe? cafe]) {
    final nombreController = TextEditingController(text: cafe?.nombre);
    final precioController = TextEditingController(text: cafe?.precio.toString());
    final stockController = TextEditingController(text: cafe?.stock.toString());

    showModalBottomSheet(
      context: context,
      isScrollControlled: true,
      builder: (_) => Padding(
        padding: EdgeInsets.only(bottom: MediaQuery.of(context).viewInsets.bottom, left: 20, right: 20, top: 20),
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            TextField(controller: nombreController, decoration: const InputDecoration(labelText: 'Nombre del Café')),
            TextField(controller: precioController, decoration: const InputDecoration(labelText: 'Precio'), keyboardType: TextInputType.number),
            TextField(controller: stockController, decoration: const InputDecoration(labelText: 'Stock'), keyboardType: TextInputType.number),
            const SizedBox(height: 20),
            ElevatedButton(
              style: ElevatedButton.styleFrom(backgroundColor: CaffeTheme.primaryCoffee),
              onPressed: () {
                final newCafe = Cafe(
                  id: cafe?.id,
                  nombre: nombreController.text,
                  precio: double.parse(precioController.text),
                  stock: int.parse(stockController.text),
                );
                cafe == null ? _skill.addCafe(newCafe) : _skill.updateCafe(newCafe);
                Navigator.pop(context);
              },
              child: Text(cafe == null ? 'Agregar' : 'Actualizar', style: const TextStyle(color: Colors.white)),
            )
          ],
        ),
      ),
    );
  }
}
```

### El Punto de Entrada (`lib/main.dart`)
```dart
import 'package:flutter/material.dart';
import 'package:firebase_core/firebase_core.dart';
import 'ui/home_page.dart';
import 'ui/theme.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp(); // Asegúrate de tener configurado google-services.json
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Caffenio App',
      debugShowCheckedModeBanner: false,
      theme: CaffeTheme.theme,
      home: HomePage(),
    );
  }
}
```
