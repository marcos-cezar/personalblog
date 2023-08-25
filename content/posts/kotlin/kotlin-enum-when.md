---
title: "Estrutura de escolha when com enum"
date: 2023-08-24
draft: false
---

Para implementarmos pontos de múltipla escolha na estrutura do nosso código com Kotlin podemos utilizar a estrutura “when”. Para os programadores que vieram do Java ele tem um aspecto semelhante ao switch, porém mais poderoso. 

Nesse texto vamos ver o tipo Enum como declarar e utilizar, em seguida vamos ver a estrutura when e aprender a utilizá-la. No final, vamos concluir com um exemplo que vai unir os dois conceitos e assim você terá mais um recurso na sua “caixa de ferramentas” de programador Kotlin que é o que você deseja se tornar.

## Enums em Kotlin

Primeiramente o enum é um tipo especial que serve para representar valores através de nomes. Então vamos precisar de enums quando a gente necessitar que determinados valores dentro do nosso problema sejam nomeados.

Vamos ver um exemplo de declaração de enums em Kotlin

```kotlin
enum class Color(val r: Int, val g: Int, val b: Int) { 
	RED(255, 0, 0),
	GREEN(0, 255, 0),
	BLUE(0, 0, 255)
}
```

No Kotlin a palavra enum é uma palavra reservada assim como no Java.  Porém, um pouco mais verboso pois é adicionada a palavra reservada class. Isso se dá por conta do poder extra que o enum em Kotlin possui.

No Kotlin o enum é uma classe comum podendo ter métodos e propriedades. Então as regras de declaração desses tipos são as mesmas. A diferença vem na hora de declarar as constantes. Que precisam de valores concretos para serem utilizados. Como é possível observar no exemplo abaixo vou adicionar um método no enum então nossa classe ficaria assim:

```kotlin
enum class Color(val r: Int, val g: Int, val b: Int) {
	RED(255, 0, 0),
	GREEN(0, 255, 0),
	BLUE(0, 0, 255);

	fun getColorNameInPtBr(color: Color): String {}
}
```

Repare que para separar a declaração das constantes dos métodos é utilizado o ponto e vírgula. Assim se declara enums em Kotlin vamos agora ver o comando when e encaixar o enum na sua estrutura.

## **************************Usando o when com enum**************************

O when é uma estrutura de decisão que permite a declaração de múltiplas ramificações. Em outras palavras, podemos usar o when quando precisamos dentro de um conjunto de opções selecionar uma e então executar nossos comandos. 

Para construir o comando when começamos com a palavra reservada when e passamos o valor a ser verificado no parêntese como no exemplo

```kotlin
fun getColorNameInPtBr(color: Color): String {
	return when (color) {
		Color.RED -> "Vermelho"
		Color.GREEN -> "Verde"
		else -> "BLUE"
	} 
}
```

o comando vai verificar o valor de color comparar com os valores configurados à esquerda da seta e rodar a ramificação a direita da seta. Aqui não é preciso colocar break para executar somente o trecho que foi selecionado pelo when. Isso é um ponto de atenção na estrutura switch do Java.

É possível também combinar valores no when, por exemplo:

```kotlin
fun getColorNameInPtBr(color: Color): String {
	return when (color) {
		Color.RED -> "Vermelho"
		Color.GREEN -> "Verde"
		Color.BLUE, Color.PURPLE -> "Ou azul ou roxo"
		else -> "BLUE"
	} 
}
```

O when de Kotlin difere também do switch em Java no quesito da flexibilidade dos valores aceitos. É possível passar qualquer objeto para ser comparado, enquanto que no Java antes da versão 12 só aceitava constantes enum, strings ou números.

É possível também declarar o when sem nenhum argumento e escrever as validações direto no lado esquerdo da seta. Podemos ver um exemplo abaixo

```kotlin
fun mixBasicColors(primaryColor: Color, secondaryColor: Color): Color {
	return when {
		(primaryColor == Color.RED && secondaryColor == Color.GREEN) -> Color.YELLOW
		(primaryColor == Color.GREEN && secondaryColor == Color.BLUE) -> Color.CYAN
		else -> Color.UNKOWN
	}
}
```

Quando nenhum valor é passado no when à esquerda da seta sempre é uma expressão booleana.

## Smart cast

O compilador Kotlin possui um esquema de inferência de tipos inteligente, onde a depender do contexto ele sabe exatamente qual o objeto em questão e consegue omitir os famosos casts muito comuns na linguagem Java. Aí vai um exemplo

```kotlin
val str: String? = "Some string sequence"
when (str) {
	is String -> str.length // Aqui já houve o cast automático para String não nula 
}
```

E o when se beneficia desse recurso. Por fim é possível utilizar o when como uma expressão, assim como nos exemplos anteriores podemos fazer

```kotlin
val colorResult = when (color) {
	Color.RED -> "Vermelho"
	Color.GREEN -> "Verde"
	else -> "Desconhecida"
} 
```

A última linha do bloco deve ser o valor retornado. No caso de utilizar o when como expressão o else então se torna obrigatório, pois uma expressão necessariamente precisa retornar um valor. 

Com isso aprendemos mais um recurso dentro da linguagem Kotlin para expressarmos nossas soluções. Espero que tenha enriquecido vocês e até logo.

