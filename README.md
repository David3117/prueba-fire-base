import firebase_admin
from firebase_admin import credentials, db

# Cargo el certificado de mi proyecto Firebase
firebase_sdk = credentials.Certificate('C:/Users/elmij/Downloads/enlace-ea430-firebase-adminsdk-rqqfw-f6204d6f4e.json')

# Hacemos referencia a la base de datos en tiempo real de Firebase
firebase_admin.initialize_app(firebase_sdk, {'databaseURL': 'https://enlace-ea430-default-rtdb.firebaseio.com/'})

# Crear una referencia a la colección 'Dispositivos'
ref = db.reference('/Dispositivos')

# Agregar un dispositivo IoT
ref.push({
    'tipo': 'Sensor de Temperatura',
    'marca': 'DHT',
    'modelo': 'DHT22',
    'rango_temperatura': '-40 a 80 °C',
    'precisión': '±0.5 °C'
})

# Modificar un dato
dispositivo_id = '-MaFn1KcapAfUV5pySlt'  # Asegúrate de que este ID exista
dispositivo_ref = ref.child(dispositivo_id)
dispositivo_ref.update({'precisión': '±0.3 °C'})

# Agregar un dispositivo más
ref.push({
    'tipo': 'Controlador de Luz',
    'marca': 'Philips',
    'modelo': 'Hue',
    'protocolos': 'Zigbee, Bluetooth'
})

# Modificar varios datos
updates = {
    dispositivo_id + '/marca': 'Xiaomi',
    '-MaFqRpuosa361LoJ-v/modelo': 'Mi Temperature and Humidity Monitor'
}
ref.update(updates)

# Apartado para evidenciar los registros

# Consulta 1: Obtener todas las entradas
all_entries = ref.get()
print("\nTodas las entradas:")
print(all_entries)

# Consulta 2: Dispositivos con rango de temperatura específico
try:
    query_rango = ref.order_by_child('rango_temperatura').equal_to('-40 a 80 °C').get()
    print("\nDispositivos con rango de temperatura -40 a 80 °C:")
    print(query_rango)
except firebase_admin.exceptions.InvalidArgumentError as e:
    print("\nError en la consulta por rango de temperatura:", e)

# Consulta 3: Dispositivos con precisión mejor que ±0.3 °C
try:
    query_precisión = ref.order_by_child('precisión').equal_to('±0.3 °C').get()
    print("\nDispositivos con precisión ±0.3 °C:")
    print(query_precisión)
except firebase_admin.exceptions.InvalidArgumentError as e:
    print("\nError en la consulta por precisión:", e)

# Ejemplo de entrada de registro

# Agregar un nuevo dispositivo de ejemplo
nuevo_dispositivo = {
    'tipo': 'Sensor de Humedad',
    'marca': 'Bosch',
    'modelo': 'BME280',
    'rango_humedad': '0 a 100 %',
    'precisión': '±3 %'
}
nuevo_dispositivo_ref = ref.push(nuevo_dispositivo)

# Mostrar el ID del nuevo dispositivo añadido
print("\nNuevo dispositivo añadido con ID:", nuevo_dispositivo_ref.key)

# Consulta para verificar la entrada del nuevo dispositivo
nuevo_dispositivo_id = nuevo_dispositivo_ref.key
consulta_nuevo_dispositivo = ref.child(nuevo_dispositivo_id).get()
print("\nConsulta del nuevo dispositivo añadido:")
print(consulta_nuevo_dispositivo)
