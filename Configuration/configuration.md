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
