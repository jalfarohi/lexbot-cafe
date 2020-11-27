# lexbot-cafe
Como construir un bot con Amazon Lex en español

# Guia para hacer un bot en Amazon Lex
 Bot es un bot de chat transaccional que puede ayudarle a pedir un café. Este tutorial le ayudará a construir CoffeeBot usando Amazon Lex y Lambda para que pueda tener una conversación como la siguiente:

  > Usuario: ¿Quiero ordenar un mocha? <br/> 
  > CoffeeBot: ¿Qué tamaño? ¿Pequeño, mediano, grande? <br/> 
  > Usuario: pequeño<br/> 
  > CoffeeBot: ¿Te gustaría frio o caliente? <br/> 
  > Usuario: caliente<br/> 
  > CoffeeBot: ¿Quieres  un moca pequeño caliente. ¿Es eso correcto? <br/> 
  > Usuario: Que sea un moca grande<br/> 
  > CoffeeBot: Te gustaría que ordene un moca grande. ¿Es eso correcto? <br/> 
  > Usuario: sí<br/> 
  > CoffeeBot: ¡Genial! Su moca estará disponible en unos minutos. ¡Gracias por usar CoffeeBot! 
  

  ## Construyendo un bot de chat de Amazon Lex 
  
  Para construir nuestro Chat bot, vamos a utilizar varias construcciones nuevas, incluyendo `intents`, `utterances`, `fulfillment` y `slots`. Cada una de estas construcciones tiene un propósito diferente al permitir a Lex entender cómo interactuar con un usuario. <br> 
 
  ### 1. Cree el bot
  
   Desde la consola de AWS, vaya a la consola de servicio `Amazon Lex`, haga clic en `Create` y, a continuación, seleccione `Custom bot` para crear un nuevo bot. Introduzca los siguientes ajustes para su bot: 
Setting | Value
--------|--------
Nombre del bot | `CoffeeBot` 
Language | Spanish LATAM
Voice de salida | Elija cualquier voz 
Tiempo de espera de sesión | `5 min`
Análisis de sentimiento | `No` 
rol IAM | `AWSServiceRoleForLexBots`
COPPA | `No` 

Y de clic en `Create` 

Nota: Si comparte una cuenta de AWS con otras personas, añada sus iniciales al nombre del bot para hacerlo único (por ejemplo, `CoffeeBotxxx`) 


### 2. Crear una intención 

Primero crearemos un Intent de saludo con el cual el bot nos indicará lo que puede hacer. 
- Utilizando el menú de la izquierda en 'Intents', haga clic en el botón `+` para agregar un nuevo Intent 
- En la nueva ventana seleccione `Create Intent` y agregue el nombre: `saludo`. 
Nota: Si trabaja en una cuenta compartida de AWS con otras personas, añada sus iniciales al nombre de Intención para que cada una sea única (por ejemplo, `saludoXXX`).
- Haga clic en `Add` para guardar el Intent. 
- Abrimos la opcion `Response` y damos clic en `Add Message`. Ingresamos el siguiente mensaje: `Hola, soy CoffeeBot, Y puedo ayudarte a ordenar tu cafe. Que deseas tomar hoy?` y damos clic en el simbolo `+` para agregarlo.
- Vamos abajo en la pantalla y damos clic en `Save`

Ahora agregaremos otro Intent para ordenar nuestro cafe:

- Utilizando el menú de la izquierda en 'Intents', haga clic en el botón `+` para agregar un nuevo Intent 
- En la nueva ventana seleccione `Create Intent` y agregue el nombre: `cafeOrderBeverageIntent`. 
Nota: Si trabaja en una cuenta compartida de AWS con otras personas, añada sus iniciales al nombre de Intención para que cada una sea única (por ejemplo, `CafeOrderBeverageIntentXXX`).
- Haga clic en `Add` para guardar el Intent. 

Una vez creada la Intención, agregue la siguiente lista de `Utterances` a su Intención recién creada colocándolos uno a la vez en el cuadro de entrada `Sample utterances` y haciendo clic en `+` o presionando 'Enter' para guardar cada uno. 

NOTA: Es muy importante que los nombres entre { } sean iguales, Amazon Lex es sentivo a mayusculas. Y no se usen signos de interrogación.

 Enunciados | 
 -----------|
se me antoja un ​{BeverageType}​ ​{Creamer}​ ​{BeverageType}​
quiero un ​{BeverageType}​
Me gustaría un {BeverageSize} {BeverageType} |
Me puede dar un {BeverageType} | 
Me da un {BeverageSize} {Creamer} {BeverageType} | 
Puedo obtener un {BeverageSize} {BeverageTemp} {Creamer} {BeverageType} | 
Deme un {BeverageSize} {Creamer} {BeverageType} |
<br>
 
 ### 3. Crear tipos de ranuras 
 - Utilizando el menú de la izquierda en `Slot types`, haga clic en el botón `+` para agregar un nuevo slot. 
 - Seleccione `Create slot type` en la ventana emergente para crear un tipo de ranura personalizado. 
 - Introduzca el `Slot type name` y, a continuación, agregue cada valor por separado de la columna `Values` de la tabla siguiente.
 
  Para añadir cada valor a su tipo de ranura, haga clic en el botón `+` situado junto al cuadro de entrada de valor o pulse `Enter`. Cuando haya introducido todos los valores para el tipo de ranura en el que está trabajando, haga clic en el botón `Add slot to intent`. <br>
  Deberías haber creado 4 tipos de Slot en total. <br>
  Nota: Aunque se guardan con la cuenta de AWS, los tipos de ranura no aparecerán en la lista hasta que se asocien en el siguiente paso.<br>
  Si trabaja en una cuenta compartida de AWS con otras personas, añada sus iniciales al nombre del tipo de slot para que cada una sea única.

Nombre del tipo de ranura | Resolución de ranura | Valores (cada entrada en una línea separada)
------------------------- | -------------------- | --------------------------------------------
`CafeBeverageType`     |   predeterminado | `cafe` <br/> `cappuchino` <br/> `latte` <br/> `mocha` <br/> `chai` <br/> `espresso`
`CafeBeverageSize`     |   predeterminado | `infantil` <br/> `chico` <br/> `mediano` <br/> `grande`  
`CafeCreamerType` |     predeterminado | `leche descremada ` <br/> `soya`<br/> `almendra`<br/> `entera`<br/>  `deslactosada` |
`CafeBeverageTemp` | predeterminado | `caliente`<br/> `frio`

<br/>

### 4. Añadir ranuras a la intención 

En la sección 'Slots' de su CoffeeBot, hay que modificar las siguientes entradas a la lista de slots.  Se modificará  `Name` y completando el `Prompt`.  <br/>
Nota: No verá el campo 'Required' hasta que haya agregado el slot. 

Requerido | Nombre | Tipo de ranura | Prompt 
----------|--------|----------------|-------
`Sí`     | `BeverageType` | `CafeBeverageType` | `Qué tipo de bebida te gustaría? Por ejemplo, moca, chai, etc.` 
`Sí` | `BeverageSize` | `CafeBeverageSize` | `Qué tamaño? ¿Pequeño, mediano, grande?`
NO | `Creamer` | `CafeCreamerType` | `Qué tipo de leche o crema?` 
 NO| `BeverageTemp` | `CafeBeverageTemp` | `Te gustaría que helado o caliente?`


<br/>

NOTA: Importante borrar el valor que esta previamente en el `Prompt`
<br/><br/>

### 5. Establezca el mensaje de confirmación 


Expanda la sección `Confirmation prompt` y marque la casilla `Confirmation prompt`. Agregue los siguientes mensajes de confirmación: 


Tipo de confirmación | Descripción 
---------------------|-----------------
Confirmar  |  Desea que pida un {BeverageSize} {BeverageType}. ¿Es eso correcto?
Cancelar   |  Muy bien. No hay nada que ordenar esta vez. ¡Nos vemos la próxima vez! 

<br/>

### 6. Definir gestión logística 

En la sección `Fulfillment`, seleccione `Return parameters to client` para realizar pruebas sencillas. Esta configuración cambiará más adelante. 

<br/>
### 7. Definir respuestas 

Expanda la sección `Response` y seleccione `Add Message` para agregar el siguiente mensaje de cierre a la intención. 

#### Mensaje de respuesta 

```Gracias. Se ha pedido {BeverageType}. ```

Haga clic en el botón `+` para agregar la respuesta. 

Marque la casilla `Wait for the user reply` e ingrese el siguiente mensaje, haciendo clic en `+` cuando haya terminado: 

#### Wait for the user reply
```OK. Gracias. ¡Que tengas un gran día! ```

Haga clic en el botón `Save Intent` en la parte inferior de la página. 


### 8. Revise la configuración de gestión de errores 

Después de guardar el Intento, seleccione `Error Handling` en el menú de la izquierda hasta abajo. 
- En `Clarification prompt` marque la cruz para quitar el texto en ingles y agregue el texto `Disculpe, puede repetir?` y de clic en el simbolo de `+` para agregarlo.
- En `Hang-up phrase` marque la cruz para quitar el texto en ingles y agregue `Lo siento, no le entendi. Adios`  y de clic en el simbolo de `+` para agregarlo.
- Cambie `Maximum number of retries` por `2`. 

<br/>

Haga clic en `Save` cuando haya confirmado que su configuración coincide con los anteriores. 

### 9. Construye y prueba el bot 

Construye el chatbot haciendo clic en el botón `Build` en la parte superior derecha de la consola. Una vez completada la compilación, podrás probar el bot con los enunciados que has introducido anteriormente. El panel `Test bot` en el lado derecho te permitirá chatear con el bot en el cuadro de entrada 'Chatear con su bot... '. Por ejemplo, si dices `¿Puedo pedir un chai?`, ¿Lex mapea correctamente `chai` a la ranura `BeverageType`? 


## Ejercicio

Ahora hay que crear un nuevo Intent pero para ordenar un sandwich con las siguientes opciones:


Nombre del tipo de ranura | Resolución de ranura | Valores (cada entrada en una línea separada)
------------------------- | -------------------- | --------------------------------------------
`TortaType`     |   predeterminado | `jamon` <br/> `milanesa` <br/> `huevo` <br/> `chorizo` <br/> `pollo`
`TortaSize`     |   predeterminado | `mediana` <br/> `grandee` <br/> `jumbo` 
`TortaQueso` |     predeterminado | `Queso blanco` <br/> `Amarillo`<br/>  |
`TortaChile` | predeterminado | `Rajas`<br/> `Chipotle`

