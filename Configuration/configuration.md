
# DataEngineering

Configuraciones de cosas tediosas que podamos automatizar.


-----------------
**Ejecutar pipeline de Data Factory**

	- Uso: llama un pipeline de data factory con parametros, logueando como usuario o como service principal

	- Palabras clave: Data Factory, Azure, Pipeline, Parameters.

	- Lenguaje: Python.
<pre><code>from azure.mgmt.datafactory import DataFactoryManagementClient
from azure.common.credentials import UserPassCredentials #To login with user and pass, use this.
from azure.common.credentials import ServicePrincipalCredentials #To login with service principal (appid and client secret) use this

subscription_id = "subsID"

#Use only one of these, depending on how you want to login.
credentials = UserPassCredentials(username="user@yourdomain.com", password="yourpass") #To login with user and pass
credentials = ServicePrincipalCredentials(client_id='appid', secret='client secret', tenant='tenantid') #To login with serv ppal

adf_client = DataFactoryManagementClient(credentials, subscription_id)


rg_name = "resource group name"
df_name = "data factory name"
p_name = "pipeline name"
params = {
    "Param1":"value1",
    "Param2":"value2"
}

adf_client.pipelines.create_run(rg_name, df_name, p_name, params)
</code></pre>

---------------------

**Encriptar y desencriptar con Fernet en Python**

	- Uso: paso a paso que permite crear un archivo .bin que guardará datos de usuario y contraseña encriptados. Brinda una función para luego desencriptar dichos datos y devolverlos como String. Adaptable para encriptar y desencriptar más de dos datos.

	- Palabras clave: Encriptar, Encriptación, Fernet, Script.
	- Lenguaje: Python.
	- Autor: Iván Ingaramo.

<pre><code>from cryptography.fernet import Fernet

# Ejecutar Paso a Paso la primera vez. Seguir las instrucciones.

# Paso 1: Genero una Key

key = Fernet.generate_key()
print(key)

# Paso 2: Copiar del print(key) anterior la key y guardarla en una variable, sino el desencriptar no funcionará después

key = b'jbf43TF4f45%GFDE¨*[tr4--#dDE' # Key de ejemplo, debe mantener la b'' inicial ya que debe ser tipo bytes

# Paso 3: Este paso se ejecuta solo la primera vez. Encripto las credenciales y las guardo en el archivo .bin.
# Nótese el salto de linea \n entre el usuario y el pass. Esto es para más adelante leer por lineas el archivo.

ciphered_suite = Fernet(key)
ciphered_cred = ciphered_suite.encrypt(b'TU_USUARIO\nTU_PASSWORD')

with open(r'..\NOMBRE_DE_TU_ARCHIVO_A_ENCRIPTAR.bin', 'wb') as file_object:
    file_object.write(ciphered_cred)

# Paso 4: Borrar del script el Paso 1 y el Paso 3, sólo dejando de este último la linea ciphered_suite = Fernet(key), necesaria para desencriptar más adelante.

# Paso 5: Función para leer el archivo .bin y recuperar las credenciales encriptadas

def decrypt(nombreArchivoEncriptado):
    with open(r'..\\' + nombreArchivoEncriptado + '.bin', 'rb') as file_object:
	for line in file_object:
	    encryptedCred = line
	
	# Desencripto
	decryptedCred = (ciphered_suite.decrypt(encryptedCred))
	
	# Convierto a String
	decryptedCred = bytes(decryptedCred).decode("utf-8")
	
	# Recorro el string linea por linea y voy guardando el usuario y el password
	count = 0
	for line in decryptedCred.splitlines():
	    if count == 0:
	        user = line
	        count = count + 1
	    else:
	        pwd = line
	        
    return user, pwd

# Prueba

usuario, password = decrypt("NOMBRE_ARCHIVO_ENCRIPTADO")
</code></pre>
---------------------

