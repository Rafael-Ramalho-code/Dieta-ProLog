# Dieta em Prolog

Programa em Prolog que faz algumas perguntas sobre você e monta uma dieta para a semana inteira. Foi um trabalho da faculdade (UFPB), feito para praticar programação lógica com algo que fosse além de árvore genealógica.

## Como funciona

O programa pergunta peso, altura, idade, sexo, nível de atividade física e objetivo (emagrecer, ganhar massa ou ganhar peso). Também pergunta se você tem alguma alergia (glúten, lactose, ovo...) ou problema de saúde (diabetes, hipertensão, colesterol alto).

Com isso ele:

1. Calcula o IMC e diz a classificação
2. Calcula a taxa metabólica basal pela fórmula de Mifflin-St Jeor e multiplica pelo fator de atividade
3. Ajusta as calorias conforme o objetivo (-500 kcal para emagrecer, +300 para ganhar massa, +500 para ganhar peso), sem deixar cair abaixo de um mínimo
4. Mostra só os alimentos que você pode comer, tirando os que batem com suas alergias ou restrições
5. Você escolhe o que topa comer em cada refeição e ele distribui as calorias e calcula as gramas de cada alimento
6. Gera os 7 dias, alternando os alimentos escolhidos pra não ficar sempre a mesma coisa

Dá pra cadastrar alimentos novos na hora (informando proteína, carboidrato e gordura por 100g) e, no final, remover algum alimento e gerar a semana de novo.

## Como rodar

Precisa do [SWI-Prolog](https://www.swi-prolog.org/download/stable).

```bash
swipl dieta.pl
```

As respostas são lidas com `read/1`, então **toda resposta termina com ponto**:

```
Qual o seu peso em kg? 70.
Alergias alimentares? ... [gluten, lactose].
Deseja adicionar algum alimento novo a dieta? (sim/nao): nao.
```

## Exemplo de saída

```
Seu IMC e: 22.86
Classificacao: Peso normal

TMB (Mifflin-St Jeor): 1698.8 kcal/dia
Meta calorica diaria ajustada: 2933.1 kcal

=== Segunda-feira ===

-- Cafe da manha (aprox. 733.3 kcal) --
  - arroz_integral: 238g
  - banana: 124g
  - frango_grelhado: 133g
```

## Observação

É um projeto de faculdade. Os valores calóricos são aproximados e o programa não substitui um nutricionista.
