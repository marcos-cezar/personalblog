
---
title: "Hello World Android/Kotlin com C++"
date: 2023-08-24
draft: false
---

# Hello World Android/Kotlin com C++

Para conseguirmos rodar código C/C++ com Android podemos utilizar o NDK que é um conjunto de ferramentas usado para atingir esse objetivo. O NDK fornece bibliotecas para gerenciar atividades nativas e componentes físicos do dispositivo (sensores, dispositivos de toque, GPS e etc). Frequentemente o NDK é utilizado quando queremos mais performance e baixa latência. Ou quando queremos executar rotinas intensas do ponto de vista computacional como jogos ou simulação de física do mundo real. Ou reutilizar bibliotecas específicas do C/C++ ou suportar alguma biblioteca específica de um hardware específico que não tenha biblioteca em outra linguagem.

A ideia deste pequeno exemplo é entender como funciona a integração entre um código Android com Kotlin e um código em C/C++. A ideia do projeto é uma tela simples com uma label e um botão (label do botão é ”mudar”). A label inicialmente com a mensagem “greetings from Kotlin” e ao clicar no botão vai fazer a chamada para o método do C/C++ chamado sayHello que vai pegar a sequência de caracteres “greetings from C/C++”. 

Para iniciar essa prática é preciso já ter o Android SDK configurado instalado e funcionando. Agora acesse o menu superior do Android Studio **********Tools → SDK Manager → SDK Tools********** selecione NDK e CMake e instale esses componentes. Isso feito podemos fazer nosso projeto.

Inicialmente vamos criar um novo projeto Android, então vamos selecionar **********File → New Project → Empty Activity.********** Vai preenchendo os dados e selecionando próximo até criar o projeto. Ele vai criar um esqueleto de um App ************Hello World************ padrão. Vamos rodar para ver se está tudo funcionando corretamente. 

Agora podemos começar a implementar nosso exemplo. Vamos abrir o arquivo *MainActivity.kt.*

Vamos modificar o código para: 

```kotlin
@Composable
fun Greeting(name: () -> String, modifier: Modifier = Modifier) {
    var toggle by remember {
        mutableStateOf(false)
    }
    val fromKotlin: () -> String = {
        "greetings from Kotlin"
    }
    var text by remember {
        mutableStateOf(fromKotlin())
    }
    Box(modifier = Modifier.fillMaxSize(),
        contentAlignment = Alignment.Center) {
        Column {
            Text(
                text = text,
                modifier = modifier
            )
            Button( onClick = {
                toggle = !toggle
                text = if (toggle) {
                    name()
                } else {
                    fromKotlin()
                }
            }) {
                Text("Muda")
            }

        }
    }
}
```

Vamos remover esse trecho, pois não vamos utilizá-lo nesse exemplo:

```kotlin
@Preview(showBackground = true)
@Composable
fun GreetingPreview() {
    SampleWithButtonTheme {
        Greeting("Android")
    }
}
```

Na classe MainActivity vamos carregar dinamicamente a biblioteca que vai ser o código que iremos criar. Mudar a chamada ao composable Greeting e adicionar a assinatura do método que vai ser implementado no C/C++: 

```kotlin
class MainActivity : ComponenteActivity() {

	companion object {
		init {
			System.loadLibrary("hello")
		}
	}

  private external fun sayHello(): String

	override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            SampleWithButtonTheme {
                // A surface container using the 'background' color from the theme
                Surface(
                    modifier = Modifier.fillMaxSize(),
                    color = MaterialTheme.colorScheme.background
                ) {
                    Greeting({ sayHello() })
                }
            }
        }
    }
}
```

Agora vamos criar o nosso código em C/C++. Vamos criar um diretório na pasta dentro da raiz do projeto app/src/cpp. Dentro dessa pasta vamos colar o código:

```kotlin
#include <jni.h>

extern "C" {

    char msg[] = "greetings from C/C++";

    JNICALL JNIEXPORT jstring
    Java_br_com_razecapps_samplewithbutton_MainActivity_sayHello(JNIEnv *env,
                                                                 jobject thiz) {
        return env->NewStringUTF(msg);
    }

}
```

Em seguida vamos criar o arquivo CMakeLists.txt dentro da pasta app/. Nesse arquivo vamos configurar o caminho do arquivo cpp que criamos, indicar o nome da biblioteca e a versão mínima do CMake que vai ser utilizado.

```kotlin
cmake_minimum_required(VERSION 3.4.1)

PROJECT(hello)

add_library(hello
            SHARED
            src/main/cpp/hello.cpp)
```

Por fim, vamos executar. Se tudo correr bem devemos ver o aplicativo em execução e chaveando as chamadas entre o Kotlin e a função em C/C++.

