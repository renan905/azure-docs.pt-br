---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/24/2020
ms.openlocfilehash: 6ad2a405fc8abf2e7df2a896d5c5363b9618dda2
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96126055"
---
## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas deste artigo, você precisará do aplicativo do Azure IoT Central a seguir criado por meio do modelo de **Aplicativo personalizado**. Para obter mais informações, consulte o [Criar um início rápido de aplicativo](../articles/iot-central/core/quick-deploy-iot-central.md). O aplicativo precisa ter sido criado em 14 de julho de 2020 ou após essa data.

Você pode executar este tutorial no Linux ou no Windows. Os comandos do shell neste tutorial seguem a convenção do Linux para os separadores de caminho '`/`', se você está acompanhando no Windows, não deixe de trocar esses separadores por '`\`'.

Os pré-requisitos diferem por sistema operacional:

### <a name="linux"></a>Linux

Este tutorial pressupõe que você esteja usando o Ubuntu Linux. As etapas deste tutorial foram testadas com o Ubuntu 18.04.

Para concluir este tutorial no Linux, instale o seguinte software em um ambiente Linux local:

Instale o **GCC**, o **Git**, o **CMake** e todas as dependências exigidas usando o comando `apt-get`:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Verifique se a versão do `cmake` é superior a **2.8.12** e se a versão do **GCC** é superior a **4.4.7**.

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows

Para realizar este tutorial no Windows, instale o seguinte software em um ambiente Windows local:

* [Visual Studio (Community, Professional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) – inclua a carga de trabalho **Desenvolvimento para Desktop com C++** ao [instalar](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) o Visual Studio.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

## <a name="download-the-code"></a>Baixar o código

Neste início rápido, você preparará um ambiente de desenvolvimento que pode ser usado para clonar e compilar o SDK do dispositivo Hub IoT do Azure para C.

Abra um prompt de comando no diretório de sua escolha. Execute o seguinte comando para clonar o repositório GitHub dos [SDKs de C e Bibliotecas do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c) nesta localização:

```cmd\bash
git clone https://github.com/Azure/azure-iot-sdk-c.git
cd azure-iot-sdk-c
git submodule update --init
```

Essa operação deve demorar alguns minutos.

## <a name="review-the-code"></a>Examine o código

Na cópia do SDK do Microsoft Azure IoT para C baixado anteriormente, abra o arquivo *azure-iot-sdk-c/iothub_client/samples/pnp/pnp_simple_thermostat/pnp_simple_thermostat.c* em um editor de texto.

Quando você executa o exemplo para se conectar ao IoT Central, ele usa o DPS (Serviço de Provisionamento de Dispositivos) para registrar o dispositivo e gerar uma cadeia de conexão. O exemplo recupera as informações de conexão do DPS necessárias por meio do ambiente de linha de comando.

A função `main` primeiro chama `CreateAndConfigureDeviceClientHandleForPnP` para:

* Defina a ID do modelo `dtmi:com:example:Thermostat;1`.
* Use o DPS para provisionar e registrar o dispositivo.
* Cria um manipulador de comando para o comando `getMaxMinReport`.
* Cria um manipulador de atualização de propriedade para a propriedade `targetTemperature` gravável.
* Crie um identificador de cliente de dispositivo e conecte-se ao seu aplicativo do IoT Central.

Depois, a função `main` inicia um thread para enviar a telemetria periodicamente.

```c
int main(void)
{
  IOTHUB_DEVICE_CLIENT_LL_HANDLE deviceClientLL = NULL;

  if (GetConnectionSettingsFromEnvironment() == false)
  {
    LogError("Cannot read required environment variable(s)");
  }
  else if (BuildUtcTimeFromCurrentTime(g_ProgramStartTime, sizeof(g_ProgramStartTime)) == false)
  {
    LogError("Unable to output the program start time");
  }
  else if ((deviceClientLL = CreateAndConfigureDeviceClientHandleForPnP()) == NULL)
  {
    LogError("Failed creating IotHub device client");
  }
  else
  {
    LogInfo("Successfully created device client handle.  Hit Control-C to exit program\n");

    int numberOfIterations = 0;
    SendMaxTemperatureSinceReboot(deviceClientLL);

    while (true)
    {
      // Wake up periodically to poll.  Even if we do not plan on sending telemetry, we still need to poll periodically in order to process
      // incoming requests from the server and to do connection keep alives.
      if ((numberOfIterations % g_sendTelemetryPollInterval) == 0)
      {
        Thermostat_SendCurrentTemperature(deviceClientLL);
      }

      IoTHubDeviceClient_LL_DoWork(deviceClientLL);
      ThreadAPI_Sleep(g_sleepBetweenPollsMs);
      numberOfIterations++;
    }

      // Clean up the iothub sdk handle
      IoTHubDeviceClient_LL_Destroy(deviceClientLL);
      // Free all the IoT SDK subsystem
      IoTHub_Deinit();
  }

  return 0;
}
```

A função `Thermostat_SendCurrentTemperature` mostra como o dispositivo envia a telemetria de temperatura para o IoT Central:

```c
void Thermostat_SendCurrentTemperature(IOTHUB_DEVICE_CLIENT_LL_HANDLE deviceClientLL) 
{
  IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
  IOTHUB_CLIENT_RESULT iothubResult;

  char temperatureStringBuffer[32];

  if (snprintf(temperatureStringBuffer, sizeof(temperatureStringBuffer), g_temperatureTelemetryBodyFormat, g_currentTemperature) < 0)
  {
    LogError("snprintf of current temperature telemetry failed");
  }
  else if ((messageHandle = IoTHubMessage_CreateFromString(temperatureStringBuffer)) == NULL)
  {
    LogError("IoTHubMessage_CreateFromString failed");
  }
  else if ((iothubResult = IoTHubDeviceClient_LL_SendEventAsync(deviceClientLL, messageHandle, NULL, NULL)) != IOTHUB_CLIENT_OK)
  {
    LogError("Unable to send telemetry message, error=%d", iothubResult);
  }

  IoTHubMessage_Destroy(messageHandle);
}
```

A função `SendMaxTemperatureSinceReboot` envia uma atualização da propriedade `maxTempSinceLastReboot` para o IoT Central:

```c
static void SendMaxTemperatureSinceReboot(IOTHUB_DEVICE_CLIENT_LL_HANDLE deviceClientLL)
{
  IOTHUB_CLIENT_RESULT iothubClientResult;
  char maxTemperatureSinceRebootProperty[256];

  if (snprintf(maxTemperatureSinceRebootProperty, sizeof(maxTemperatureSinceRebootProperty), g_maxTemperatureSinceRebootFormat, g_maxTemperature) < 0)
  {
      LogError("snprintf building maxTemperature failed");
  }
  else if ((iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(deviceClientLL, (const unsigned char*)maxTemperatureSinceRebootProperty, strlen(maxTemperatureSinceRebootProperty), NULL, NULL)) != IOTHUB_CLIENT_OK)
  {
      LogError("Unable to send reported state for maximum temperature.  Error=%d", iothubClientResult);
  }
  else
  {
      LogInfo("Sending maxTempSinceReboot property");
  }
}
```

A função `Thermostat_DeviceTwinCallback` processa as atualizações de propriedade gravável por meio do IoT Central:

```c
static void Thermostat_DeviceTwinCallback(DEVICE_TWIN_UPDATE_STATE updateState, const unsigned char* payload, size_t size, void* userContextCallback)
{
  // The device handle associated with this request is passed as the context, since we will need to send reported events back.
  IOTHUB_DEVICE_CLIENT_LL_HANDLE deviceClientLL = (IOTHUB_DEVICE_CLIENT_LL_HANDLE)userContextCallback;

  char* jsonStr = NULL;
  JSON_Value* rootValue = NULL;
  JSON_Object* desiredObject;
  JSON_Value* versionValue = NULL;
  JSON_Value* targetTemperatureValue = NULL;

  LogInfo("DeviceTwin callback invoked");

  if ((jsonStr = CopyTwinPayloadToString(payload, size)) == NULL)
  {
    LogError("Unable to allocate twin buffer");
  }
  else if ((rootValue = json_parse_string(jsonStr)) == NULL)
  {
    LogError("Unable to parse twin JSON");
  }
  else if ((desiredObject = GetDesiredJson(updateState, rootValue)) == NULL)
  {
    LogError("Cannot retrieve desired JSON object");
  }
  else if ((targetTemperatureValue = json_object_get_value(desiredObject, g_JSONTargetTemperature)) == NULL)
  {
    LogInfo("JSON property %s not specified.  This is NOT an error as the server doesn't need to set this, but there is no further action to take.", g_JSONTargetTemperature);
  }
  else if ((versionValue = json_object_get_value(desiredObject, g_IoTHubTwinDesiredVersion)) == NULL)
  {
    // The $version does need to be set in *any* legitimate twin desired document.  Its absence suggests 
    // something is fundamentally wrong with how we've received the twin and we should not proceed.
    LogError("Cannot retrieve field %s for twin.  The underlying IoTHub device twin protocol (NOT the service solution directly) should have specified this.", g_IoTHubTwinDesiredVersion);
  }
  else if (json_value_get_type(versionValue) != JSONNumber)
  {
    // The $version must be a number (and in practice an int) A non-numerical value indicates 
    // something is fundamentally wrong with how we've received the twin and we should not proceed.
    LogError("JSON field %s is not a number but must be", g_IoTHubTwinDesiredVersion);
  }
  else if (json_value_get_type(targetTemperatureValue) != JSONNumber)
  {
    LogError("JSON field %s is not a number", g_JSONTargetTemperature);
  }
  else
  {
    double targetTemperature = json_value_get_number(targetTemperatureValue);
    int version = (int)json_value_get_number(versionValue);

    LogInfo("Received targetTemperature = %f", targetTemperature);

    bool maxTempUpdated = false;
    UpdateTemperatureAndStatistics(targetTemperature, &maxTempUpdated);

    // The device needs to let the service know that it has received the targetTemperature desired property.
    SendTargetTemperatureReport(deviceClientLL, targetTemperature, g_statusSuccess, version, g_temperaturePropertyResponseDescription);

    if (maxTempUpdated)
    {
      // If the Maximum temperature has been updated, we also report this as a property.
      SendMaxTemperatureSinceReboot(deviceClientLL);
    }
  }

  json_value_free(rootValue);
  free(jsonStr);
}
```

A função `Thermostat_DeviceMethodCallback` processa os comandos chamados por meio do IoT Central:

```c
static int Thermostat_DeviceMethodCallback(const char* methodName, const unsigned char* payload, size_t size, unsigned char** response, size_t* responseSize, void* userContextCallback)
{
  (void)userContextCallback;

  char* jsonStr = NULL;
  JSON_Value* rootValue = NULL;
  const char* sinceStr;
  int result;

  LogInfo("Device method %s arrived", methodName);

  *response = NULL;
  *responseSize = 0;

  if (strcmp(methodName, g_getMaxMinReport) != 0)
  {
    LogError("Method name %s is not supported on this component", methodName);
    result = g_statusNotFoundStatus;
  }
  else if ((jsonStr = CopyTwinPayloadToString(payload, size)) == NULL)
  {
    LogError("Unable to allocate twin buffer");
    result = g_statusInternalError;
  }
  else if ((rootValue = json_parse_string(jsonStr)) == NULL)
  {
    LogError("Unable to parse twin JSON");
    result = g_statusBadFormat;
  }
  // See caveats section in ../readme.md; we don't actually respect this sinceStr to keep the sample simple,
  // but want to demonstrate how to parse out in any case.
  else if ((sinceStr = json_value_get_string(rootValue)) == NULL)
  {
    LogError("Cannot retrieve since value");
    result = g_statusBadFormat;
  }
  else if (BuildMaxMinCommandResponse(response, responseSize) == false)
  {
    LogError("Unable to build response");
    result = g_statusInternalError;
  }
  else
  {
    LogInfo("Returning success from command request");
    result = g_statusSuccess;
  }

  if (*response == NULL)
  {
    SetEmptyCommandResponse(response, responseSize, &result);
  }

  json_value_free(rootValue);
  free(jsonStr);

  return result;
}
```

## <a name="build-the-code"></a>Compilar o código

Use o SDK do dispositivo para criar o código de exemplo incluído:

1. Crie um subdiretório _cmake_ na pasta raiz do SDK do dispositivo e navegue até essa pasta:

    ```cmd\bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Execute os seguintes comandos para criar o SDK e os exemplos:

    ```cmd\bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key=ON -Drun_e2e_tests=OFF ..
    cmake --build .
    ```

## <a name="get-connection-information"></a>Obter informações de conexão

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>Executar o código

Para executar o aplicativo de exemplo, abra um ambiente de linha de comando e acesse a pasta *azure-iot-sdk-c\cmake*.

[!INCLUDE [iot-central-connection-environment](iot-central-connection-environment.md)]

Para executar o exemplo:

```bash
# Bash
cd iothub_client/samples/pnp/pnp_simple_thermostat/
./pnp_simple_thermostat
```

```cmd
REM Windows
cd iothub_client\samples\pnp\pnp_simple_thermostat\Debug
.\pnp_simple_thermostat.exe
```

A saída a seguir mostra o dispositivo se registrando e se conectando ao IoT Central. O exemplo começa a enviar a telemetria:

```output
Info: Initiating DPS client to retrieve IoT Hub connection information
-> 11:53:07 CONNECT | VER: 4 | KEEPALIVE: 0 | FLAGS: 194 | USERNAME: 0ne001BB295/registrations/sample-device-01/api-version=2019-03-31&ClientVersion=1.3.9 | PWD: XXXX | CLEAN: 1
<- 11:53:08 CONNACK | SESSION_PRESENT: false | RETURN_CODE: 0x0
-> 11:53:09 SUBSCRIBE | PACKET_ID: 1 | TOPIC_NAME: $dps/registrations/res/# | QOS: 1
<- 11:53:10 SUBACK | PACKET_ID: 1 | RETURN_CODE: 1
-> 11:53:10 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_MOST_ONCE | TOPIC_NAME: $dps/registrations/PUT/iotdps-register/?$rid=1 | PAYLOAD_LEN: 91
<- 11:53:11 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_MOST_ONCE | TOPIC_NAME: $dps/registrations/res/202/?$rid=1&retry-after=3 | PACKET_ID: 2 | PAYLOAD_LEN: 94
-> 11:53:12 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_MOST_ONCE | TOPIC_NAME: $dps/registrations/GET/iotdps-get-operationstatus/?$rid=2&operationId=4.2f792ade0a5c3e68.e123be1d-3b1e-4874-813a-5612ae586979 | PAYLOAD_LEN: 91
<- 11:53:13 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_MOST_ONCE | TOPIC_NAME: $dps/registrations/res/200/?$rid=2 | PACKET_ID: 2 | PAYLOAD_LEN: 478
Info: Provisioning callback indicates success.  iothubUri=iotc-5d1e8352-cb4d-4e0b-ac00-37704ffa1f2a.azure-devices.net, deviceId=sample-device-01
-> 11:53:13 DISCONNECT
Info: DPS successfully registered.  Continuing on to creation of IoTHub device client handle.
Info: Successfully created device client handle.  Hit Control-C to exit program

Info: Sending maxTempSinceReboot property
-> 11:53:14 CONNECT | VER: 4 | KEEPALIVE: 240 | FLAGS: 192 | USERNAME: iotc-5d1e8352-cb4d-4e0b-ac00-37704ffa1f2a.azure-devices.net/sample-device-01/?api-version=2020-09-30&DeviceClientType=iothubclient%2f1.3.9%20(native%3b%20WindowsProduct%3a0x00000004%206.2%3b%20x64%3b%20%7bC85D6F43-30FF-4647-BF03-226E8A2943FD%7d)&model-id=dtmi%3acom%3aexample%3aThermostat%3b1 | PWD: XXXX | CLEAN: 0
<- 11:53:14 CONNACK | SESSION_PRESENT: false | RETURN_CODE: 0x0
-> 11:53:15 SUBSCRIBE | PACKET_ID: 2 | TOPIC_NAME: $iothub/twin/res/# | QOS: 0 | TOPIC_NAME: $iothub/methods/POST/# | QOS: 0
<- 11:53:15 SUBACK | PACKET_ID: 2 | RETURN_CODE: 0 | RETURN_CODE: 0
-> 11:53:15 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_MOST_ONCE | TOPIC_NAME: $iothub/twin/GET/?$rid=3
-> 11:53:15 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_MOST_ONCE | TOPIC_NAME: $iothub/twin/PATCH/properties/reported/?$rid=4 | PAYLOAD_LEN: 32
-> 11:53:15 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_LEAST_ONCE | TOPIC_NAME: devices/sample-device-01/messages/events/ | PACKET_ID: 5 | PAYLOAD_LEN: 21
<- 11:53:15 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: QOS_VALUE_INVALID | TOPIC_NAME: $iothub/twin/res/204/?$rid=4&$version=2 | PAYLOAD_LEN: 0
<- 11:53:15 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: QOS_VALUE_INVALID | TOPIC_NAME: $iothub/twin/res/200/?$rid=3 | PAYLOAD_LEN: 82
Info: DeviceTwin callback invoked
Info: JSON property targetTemperature not specified.  This is NOT an error as the server doesn't need to set this, but there is no further action to take.
<- 11:53:15 PUBACK | PACKET_ID: 5
-> 11:53:15 SUBSCRIBE | PACKET_ID: 6 | TOPIC_NAME: $iothub/twin/PATCH/properties/desired/# | QOS: 0
<- 11:53:16 SUBACK | PACKET_ID: 6 | RETURN_CODE: 0
-> 11:54:20 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_LEAST_ONCE | TOPIC_NAME: devices/sample-device-01/messages/events/ | PACKET_ID: 7 | PAYLOAD_LEN: 21
<- 11:54:21 PUBACK | PACKET_ID: 7
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

Você pode ver como o dispositivo responde a comandos e a atualizações de propriedade:

```output
<- 11:56:34 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: QOS_VALUE_INVALID | TOPIC_NAME: $iothub/methods/POST/getMaxMinReport/?$rid=1 | PAYLOAD_LEN: 26
Info: Device method getMaxMinReport arrived
Info: Response=<{"maxTemp":22.00,"minTemp":22.00,"avgTemp":22.00,"startTime":"2020-11-25T11:53:05Z","endTime":"2020-11-25T11:56:34Z"}>
Info: Returning success from command request
-> 11:56:34 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_MOST_ONCE | TOPIC_NAME: $iothub/methods/res/200/?$rid=1 | PAYLOAD_LEN: 117

...

<- 11:57:24 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: QOS_VALUE_INVALID | TOPIC_NAME: $iothub/twin/PATCH/properties/desired/?$version=2 | PAYLOAD_LEN: 37
Info: DeviceTwin callback invoked
Info: Received targetTemperature = 56.000000
Info: Sending maxTempSinceReboot property
Info: Sending maxTempSinceReboot property
-> 11:57:24 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_MOST_ONCE | TOPIC_NAME: $iothub/twin/PATCH/properties/reported/?$rid=11 | PAYLOAD_LEN: 68
-> 11:57:24 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_MOST_ONCE | TOPIC_NAME: $iothub/twin/PATCH/properties/reported/?$rid=12 | PAYLOAD_LEN: 32
```
