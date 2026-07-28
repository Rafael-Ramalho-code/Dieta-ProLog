:- initialization(main).

main :-
    write('=== Questionario de Dieta Personalizada ==='), nl, nl,
    perguntar_peso(Peso),
    perguntar_altura(AlturaCm),
    perguntar_idade(Idade),
    perguntar_sexo(Sexo),
    perguntar_nivel_atividade(Nivel),
    perguntar_objetivo(Objetivo),
    perguntar_alergias(Alergias),
    perguntar_problemas(Problemas),
    perguntar_cadastro_alimentos,

    AlturaM is AlturaCm / 100,
    calcular_imc(Peso, AlturaM, IMC),
    classificar_e_mostrar_imc(IMC, _),

    calcular_tmb(Peso, AlturaCm, Idade, Sexo, TMB),
    nivel_atividade_fator(Nivel, Fator),
    TDEE is TMB * Fator,
    ajustar_calorias_objetivo(TDEE, Objetivo, CaloriasAjustadas),
    aplicar_piso_seguranca(Sexo, CaloriasAjustadas, CaloriasFinal),
    mostrar_resumo_calorico(TMB, TDEE, Objetivo, CaloriasFinal),

    nl, nl, write('=== ESCOLHA DOS ALIMENTOS PARA CADA REFEICAO ==='), nl,
    write('Para cada refeicao, escolha os alimentos que voce topa comer.'), nl,
    write('A dieta da semana vai alternar entre os alimentos escolhidos'), nl,
    write('em cada slot para nao ficar repetitiva.'), nl,
    montar_pools_das_refeicoes(Alergias, Problemas, Pools),

    gerar_semana(CaloriasFinal, Pools, Semana),
    nl, nl, write('=== SUA DIETA SEMANAL ==='), nl,
    format('Objetivo: ~w~n', [Objetivo]),
    mostrar_semana(Semana),
    perguntar_e_ajustar(CaloriasFinal, Pools).

perguntar_peso(Peso) :-
    write('Qual o seu peso em kg? '),
    read(Peso).

perguntar_altura(AlturaCm) :-
    write('Qual a sua altura em centimetros (ex: 175)? '),
    read(AlturaCm).

perguntar_idade(Idade) :-
    write('Qual a sua idade em anos? '),
    read(Idade).

perguntar_sexo(Sexo) :-
    nl, write('Sexo biologico (usado apenas no calculo da TMB):'), nl,
    write('1 - Masculino'), nl,
    write('2 - Feminino'), nl,
    write('Escolha (1-2): '),
    read(Op),
    traduzir_sexo(Op, Sexo).
traduzir_sexo(1, m) :- !.
traduzir_sexo(2, f) :- !.
traduzir_sexo(_, Sexo) :-
    write('Opcao invalida, tente novamente.'), nl,
    perguntar_sexo(Sexo).

perguntar_nivel_atividade(Nivel) :-
    nl, write('Nivel de atividade fisica:'), nl,
    write('1 - Sedentario (pouco ou nenhum exercicio)'), nl,
    write('2 - Leve (exercicio leve 1 a 3x/semana)'), nl,
    write('3 - Moderado (exercicio moderado 3 a 5x/semana)'), nl,
    write('4 - Intenso (exercicio pesado 6 a 7x/semana)'), nl,
    write('5 - Muito intenso (exercicio muito pesado / trabalho fisico)'), nl,
    write('Escolha (1-5): '),
    read(Op),
    traduzir_nivel(Op, Nivel).
traduzir_nivel(1, sedentario) :- !.
traduzir_nivel(2, leve) :- !.
traduzir_nivel(3, moderado) :- !.
traduzir_nivel(4, intenso) :- !.
traduzir_nivel(5, muito_intenso) :- !.
traduzir_nivel(_, Nivel) :-
    write('Opcao invalida, tente novamente.'), nl,
    perguntar_nivel_atividade(Nivel).

perguntar_objetivo(Objetivo) :-
    nl, write('Qual o seu objetivo?'), nl,
    write('1 - Emagrecer'), nl,
    write('2 - Ganhar massa muscular'), nl,
    write('3 - Ganhar peso'), nl,
    write('Escolha (1-3): '),
    read(Op),
    traduzir_objetivo(Op, Objetivo).
traduzir_objetivo(1, emagrecer) :- !.
traduzir_objetivo(2, ganhar_massa) :- !.
traduzir_objetivo(3, ganhar_peso) :- !.
traduzir_objetivo(_, Objetivo) :-
    write('Opcao invalida, tente novamente.'), nl,
    perguntar_objetivo(Objetivo).

perguntar_alergias(Alergias) :-
    nl, write('Alergias alimentares? Opcoes: leite, gluten, amendoim, ovo, soja, frutos_do_mar, lactose'), nl,
    write('Digite em lista, ex: [leite, gluten]. Se nenhuma, digite []: '),
    read(Alergias).

perguntar_problemas(Problemas) :-
    nl, write('Problemas de saude? Opcoes: diabetes, hipertensao, colesterol_alto'), nl,
    write('Digite em lista, ex: [diabetes]. Se nenhum, digite []: '),
    read(Problemas).

calcular_imc(Peso, Altura, IMC) :-
    IMC is Peso / (Altura * Altura).

classificar_e_mostrar_imc(IMC, Classificacao) :-
    classificar_imc(IMC, Classificacao),
    nl, format('Seu IMC e: ~2f~n', [IMC]),
    format('Classificacao: ~w~n', [Classificacao]).

classificar_imc(IMC, 'Abaixo do peso') :- IMC < 18.5, !.
classificar_imc(IMC, 'Peso normal') :- IMC >= 18.5, IMC < 25, !.
classificar_imc(IMC, 'Sobrepeso') :- IMC >= 25, IMC < 30, !.
classificar_imc(_, 'Obesidade').

calcular_tmb(Peso, AlturaCm, Idade, m, TMB) :-
    TMB is 10 * Peso + 6.25 * AlturaCm - 5 * Idade + 5.
calcular_tmb(Peso, AlturaCm, Idade, f, TMB) :-
    TMB is 10 * Peso + 6.25 * AlturaCm - 5 * Idade - 161.

nivel_atividade_fator(sedentario, 1.2).
nivel_atividade_fator(leve, 1.375).
nivel_atividade_fator(moderado, 1.55).
nivel_atividade_fator(intenso, 1.725).
nivel_atividade_fator(muito_intenso, 1.9).

ajustar_calorias_objetivo(TDEE, emagrecer, Cal) :- Cal is TDEE - 500.
ajustar_calorias_objetivo(TDEE, ganhar_massa, Cal) :- Cal is TDEE + 300.
ajustar_calorias_objetivo(TDEE, ganhar_peso, Cal) :- Cal is TDEE + 500.

aplicar_piso_seguranca(m, Cal, CalFinal) :- CalFinal is max(Cal, 1500).
aplicar_piso_seguranca(f, Cal, CalFinal) :- CalFinal is max(Cal, 1200).

mostrar_resumo_calorico(TMB, TDEE, Objetivo, CaloriasFinal) :-
    nl,
    format('TMB (Mifflin-St Jeor): ~1f kcal/dia~n', [TMB]),
    format('Gasto calorico total (TDEE = TMB x fator de atividade): ~1f kcal/dia~n', [TDEE]),
    format('Objetivo: ~w~n', [Objetivo]),
    orientacao_objetivo(Objetivo, Orientacao),
    format('Orientacao: ~w~n', [Orientacao]),
    format('Meta calorica diaria ajustada: ~1f kcal~n', [CaloriasFinal]).

orientacao_objetivo(emagrecer,
    'Deficit calorico moderado (~500 kcal abaixo do TDEE), priorize proteinas magras e vegetais.').
orientacao_objetivo(ganhar_massa,
    'Superavit calorico leve (~300 kcal acima do TDEE) com alta ingestao de proteina e treino de forca.').
orientacao_objetivo(ganhar_peso,
    'Superavit calorico (~500 kcal acima do TDEE) com foco em carboidratos e gorduras boas.').

:- dynamic alimento/3.
:- dynamic alimento_kcal/2.

alimento(arroz_integral, carboidrato, []).
alimento(batata_doce, carboidrato, []).
alimento(batata_inglesa_cozida, carboidrato, []).
alimento(macarrao_cozido, carboidrato, [gluten]).
alimento(aveia, carboidrato, [gluten]).
alimento(pao_integral, carboidrato, [gluten]).
alimento(quinoa, carboidrato, []).
alimento(cuscuz, carboidrato, []).
alimento(feijao, carboidrato, []).

alimento(frango_grelhado, proteina, []).
alimento(carne_vermelha_magra, proteina, []).
alimento(carne_gorda, proteina, []).
alimento(ovo, proteina, [ovo]).
alimento(peixe, proteina, [frutos_do_mar]).
alimento(atum, proteina, [frutos_do_mar]).
alimento(sardinha, proteina, [frutos_do_mar]).
alimento(whey_protein, proteina, [leite, lactose]).

alimento(leite_desnatado, laticinio, [leite, lactose]).
alimento(iogurte_natural, laticinio, [leite, lactose]).
alimento(queijo_branco, laticinio, [leite, lactose]).

alimento(azeite_oliva, extra, []).
alimento(abacate, extra, []).
alimento(castanhas, extra, [amendoim]).
alimento(pasta_amendoim, extra, [amendoim]).
alimento(lentilha, extra, []).
alimento(queijo_mussarela, extra, [leite, lactose]).
alimento(queijo_coalho, extra, [leite, lactose]).
alimento(requeijao_diet, extra, [leite, lactose]).
alimento(mel, extra, []).
alimento(leite_em_po, extra, [leite, lactose]).
alimento(granola, extra, [gluten]).
alimento(chia, extra, []).
alimento(linhaca, extra, []).
alimento(coco_ralado, extra, []).

alimento(brocolis, vegetal, []).
alimento(espinafre, vegetal, []).
alimento(salada_verde, vegetal, []).
alimento(cenoura, vegetal, []).
alimento(tomate, vegetal, []).

alimento(banana, fruta, []).
alimento(maca, fruta, []).
alimento(mamao, fruta, []).
alimento(uva, fruta, []).
alimento(pessego, fruta, []).
alimento(morango, fruta, []).
alimento(melancia, fruta, []).
alimento(goiaba, fruta, []).
alimento(pera, fruta, []).

alimento_kcal(arroz_integral, 123).
alimento_kcal(batata_doce, 86).
alimento_kcal(batata_inglesa_cozida, 87).
alimento_kcal(macarrao_cozido, 158).
alimento_kcal(aveia, 389).
alimento_kcal(pao_integral, 247).
alimento_kcal(quinoa, 120).
alimento_kcal(cuscuz, 112).
alimento_kcal(feijao, 127).

alimento_kcal(frango_grelhado, 165).
alimento_kcal(carne_vermelha_magra, 250).
alimento_kcal(carne_gorda, 280).
alimento_kcal(ovo, 155).
alimento_kcal(peixe, 140).
alimento_kcal(atum, 130).
alimento_kcal(sardinha, 208).
alimento_kcal(whey_protein, 400).

alimento_kcal(leite_desnatado, 35).
alimento_kcal(iogurte_natural, 61).
alimento_kcal(queijo_branco, 264).

alimento_kcal(azeite_oliva, 884).
alimento_kcal(abacate, 160).
alimento_kcal(castanhas, 607).
alimento_kcal(pasta_amendoim, 588).
alimento_kcal(lentilha, 116).
alimento_kcal(queijo_mussarela, 280).
alimento_kcal(queijo_coalho, 310).
alimento_kcal(requeijao_diet, 130).
alimento_kcal(mel, 304).
alimento_kcal(leite_em_po, 496).
alimento_kcal(granola, 471).
alimento_kcal(chia, 486).
alimento_kcal(linhaca, 534).
alimento_kcal(coco_ralado, 660).

alimento_kcal(brocolis, 34).
alimento_kcal(espinafre, 23).
alimento_kcal(salada_verde, 15).
alimento_kcal(cenoura, 41).
alimento_kcal(tomate, 18).

alimento_kcal(banana, 89).
alimento_kcal(maca, 52).
alimento_kcal(mamao, 43).
alimento_kcal(uva, 69).
alimento_kcal(pessego, 39).
alimento_kcal(morango, 32).
alimento_kcal(melancia, 30).
alimento_kcal(goiaba, 68).
alimento_kcal(pera, 57).

restrito_por_problema(batata_doce, diabetes).
restrito_por_problema(banana, diabetes).
restrito_por_problema(mamao, diabetes).
restrito_por_problema(melancia, diabetes).
restrito_por_problema(queijo_branco, hipertensao).
restrito_por_problema(queijo_mussarela, hipertensao).
restrito_por_problema(queijo_coalho, hipertensao).
restrito_por_problema(carne_vermelha_magra, colesterol_alto).
restrito_por_problema(carne_gorda, colesterol_alto).
restrito_por_problema(ovo, colesterol_alto).

alimento_seguro(Alimento, Alergias) :-
    alimento(Alimento, _, Tags),
    \+ (member(Tag, Tags), member(Tag, Alergias)).

alimento_adequado(Alimento, Alergias, Problemas) :-
    alimento_seguro(Alimento, Alergias),
    \+ (member(P, Problemas), restrito_por_problema(Alimento, P)).

alimentos_categoria(Categoria, Alergias, Problemas, Lista) :-
    findall(A,
        (alimento(A, Categoria, _), alimento_adequado(A, Alergias, Problemas)),
        Lista).

refeicao(cafe_da_manha, 'Cafe da manha', 0.25,
    [carboidrato-0.40, fruta-0.15, proteina-0.30, extra-0.15]).
refeicao(almoco, 'Almoco', 0.35,
    [carboidrato-0.35, proteina-0.35, vegetal-0.10, extra-0.20]).
refeicao(lanche, 'Lanche da tarde', 0.15,
    [fruta-0.35, proteina-0.30, extra-0.35]).
refeicao(jantar, 'Jantar', 0.25,
    [carboidrato-0.30, proteina-0.40, vegetal-0.10, extra-0.20]).

dia_semana(1, 'Segunda-feira').
dia_semana(2, 'Terca-feira').
dia_semana(3, 'Quarta-feira').
dia_semana(4, 'Quinta-feira').
dia_semana(5, 'Sexta-feira').
dia_semana(6, 'Sabado').
dia_semana(7, 'Domingo').

mostrar_opcoes_e_escolher(Lista, Escolhidos) :-
    length(Lista, N),
    forall(nth1(I, Lista, Item), format('  ~w - ~w~n', [I, Item])),
    write('Escolha o(s) numero(s) dos alimentos que voce topa comer nesse slot ao longo da semana (ex: 2. ou [1,3].): '),
    read(Entrada),
    normalizar_escolha(Entrada, Numeros),
    ( Numeros \== [], forall(member(Num, Numeros), (integer(Num), Num >= 1, Num =< N))
    -> findall(Item, (member(Num, Numeros), nth1(Num, Lista, Item)), Brutos),
       list_to_set(Brutos, Escolhidos)
    ;  write('Opcao invalida, tente novamente.'), nl,
       mostrar_opcoes_e_escolher(Lista, Escolhidos)
    ).

normalizar_escolha(Entrada, Numeros) :- is_list(Entrada), !, Numeros = Entrada.
normalizar_escolha(Entrada, [Entrada]) :- integer(Entrada), !.
normalizar_escolha(_, []).

perguntar_deseja_extra(Resposta) :-
    nl, write('Deseja incluir algum acompanhamento/extra nesta refeicao? (sim/nao): '),
    read(Resposta).

escolher_pool_slot(extra, Alergias, Problemas, Pool) :-
    !,
    perguntar_deseja_extra(Resposta),
    ( Resposta == sim
    -> escolher_pool_slot_padrao(extra, Alergias, Problemas, Pool)
    ;  format('~nOk, nenhum acompanhamento/extra sera incluido nesta refeicao.~n', []),
       Pool = []
    ).
escolher_pool_slot(Categoria, Alergias, Problemas, Pool) :-
    escolher_pool_slot_padrao(Categoria, Alergias, Problemas, Pool).

escolher_pool_slot_padrao(Categoria, Alergias, Problemas, Pool) :-
    alimentos_categoria(Categoria, Alergias, Problemas, Opcoes),
    ( Opcoes == []
    -> format('~nNenhuma opcao disponivel para ~w com suas restricoes atuais.~n', [Categoria]),
       Pool = []
    ;  format('~nOpcoes de ~w:~n', [Categoria]),
       mostrar_opcoes_e_escolher(Opcoes, Pool)
    ).

montar_pools_das_refeicoes(Alergias, Problemas, Pools) :-
    findall(Refeicao-Categoria-Escolhidos,
        ( refeicao(Refeicao, NomeExib, _, Slots),
          format('~n>>> Escolhendo alimentos para: ~w~n', [NomeExib]),
          member(Categoria-_, Slots),
          escolher_pool_slot(Categoria, Alergias, Problemas, Escolhidos)
        ),
        Pools).

item_rotativo(Lista, Offset, Item) :-
    length(Lista, Len),
    Len > 0, !,
    Idx is (Offset mod Len) + 1,
    nth1(Idx, Lista, Item).
item_rotativo(_, _, nenhum_disponivel).

gerar_semana(CaloriasFinal, Pools, Semana) :-
    findall(dia(NomeDia, RefeicoesDoDia),
        ( between(1, 7, N),
          dia_semana(N, NomeDia),
          findall(refeicao_dia(NomeRefeicao, CaloriasRefeicao, Itens),
              ( refeicao(Id, NomeRefeicao, PercRef, Slots),
                CaloriasRefeicao is CaloriasFinal * PercRef,
                montar_itens_do_dia(N, Id, Slots, CaloriasRefeicao, Pools, Itens)
              ),
              RefeicoesDoDia)
        ),
        Semana).

montar_itens_do_dia(N, Refeicao, Slots, CaloriasRefeicao, Pools, Itens) :-
    Offset is N - 1,
    findall(Alimento-QuantidadeG,
        ( member(Categoria-Percentual, Slots),
          member(Refeicao-Categoria-Escolhidos, Pools),
          item_rotativo(Escolhidos, Offset, Alimento),
          CaloriasSlot is CaloriasRefeicao * Percentual,
          calcular_quantidade(Alimento, CaloriasSlot, QuantidadeG)
        ),
        Itens).

calcular_quantidade(nenhum_disponivel, _, 0) :- !.
calcular_quantidade(Alimento, CaloriasSlot, QuantidadeG) :-
    alimento_kcal(Alimento, KcalPor100g),
    QuantidadeG is round((CaloriasSlot / KcalPor100g) * 100).

mostrar_semana(Semana) :-
    forall(member(dia(NomeDia, Refeicoes), Semana),
        ( format('~n=== ~w ===~n', [NomeDia]),
          forall(member(refeicao_dia(NomeRefeicao, CaloriasRefeicao, Itens), Refeicoes),
              ( format('~n-- ~w (aprox. ~1f kcal) --~n', [NomeRefeicao, CaloriasRefeicao]),
                mostrar_itens(Itens)
              ))
        )).

mostrar_itens(Itens) :-
    ( forall(member(Alimento-_, Itens), Alimento == nenhum_disponivel)
    -> write('  (nada escolhido para esta refeicao)'), nl
    ;  forall(member(Alimento-Quantidade, Itens),
           ( Alimento == nenhum_disponivel -> true
           ;  format('  - ~w: ~wg~n', [Alimento, Quantidade]) ))
    ).

perguntar_cadastro_alimentos :-
    nl, write('Deseja adicionar algum alimento novo a dieta? (sim/nao): '),
    read(Resposta),
    ( Resposta == sim
    -> cadastrar_alimento,
       perguntar_cadastro_alimentos
    ;  true
    ).

cadastrar_alimento :-
    nl, write('Nome do alimento (em minusculas, sem espacos, ex: pao_de_queijo): '),
    read(Nome),
    perguntar_categoria_alimento(Categoria),
    nl, write('Tags de alergia do alimento? Opcoes: leite, gluten, amendoim, ovo, soja, frutos_do_mar, lactose'), nl,
    write('Digite em lista, ex: [leite, lactose]. Se nenhuma, digite []: '),
    read(Tags),
    write('Gramas de proteina por 100g: '), read(ProteinaG),
    write('Gramas de carboidrato por 100g: '), read(CarboidratoG),
    write('Gramas de gordura por 100g: '), read(GorduraG),
    KcalPor100g is ProteinaG * 4 + CarboidratoG * 4 + GorduraG * 9,
    retractall(alimento(Nome, _, _)),
    retractall(alimento_kcal(Nome, _)),
    assertz(alimento(Nome, Categoria, Tags)),
    assertz(alimento_kcal(Nome, KcalPor100g)),
    format('~nAlimento "~w" cadastrado como ~w (~1f kcal/100g).~n', [Nome, Categoria, KcalPor100g]).

perguntar_categoria_alimento(Categoria) :-
    nl, write('Classificacao do alimento:'), nl,
    write('1 - Carboidrato'), nl,
    write('2 - Proteina'), nl,
    write('3 - Laticinio'), nl,
    write('4 - Vegetal'), nl,
    write('5 - Fruta'), nl,
    write('6 - Extra (gorduras e acompanhamentos)'), nl,
    write('Escolha (1-6): '),
    read(Op),
    traduzir_categoria_alimento(Op, Categoria).
traduzir_categoria_alimento(1, carboidrato) :- !.
traduzir_categoria_alimento(2, proteina) :- !.
traduzir_categoria_alimento(3, laticinio) :- !.
traduzir_categoria_alimento(4, vegetal) :- !.
traduzir_categoria_alimento(5, fruta) :- !.
traduzir_categoria_alimento(6, extra) :- !.
traduzir_categoria_alimento(_, Categoria) :-
    write('Opcao invalida, tente novamente.'), nl,
    perguntar_categoria_alimento(Categoria).

perguntar_e_ajustar(CaloriasFinal, Pools) :-
    nl, write('Deseja remover algum alimento das suas escolhas e gerar a semana de novo? (sim/nao): '),
    read(Resposta),
    ( Resposta == sim
    -> nl, write('Quais alimentos deseja remover das suas escolhas? Ex: [ovo, peixe]: '),
       read(Remover),
       remover_de_pools(Pools, Remover, NovosPools),
       gerar_semana(CaloriasFinal, NovosPools, NovaSemana),
       nl, write('=== DIETA SEMANAL ATUALIZADA ==='), nl,
       mostrar_semana(NovaSemana),
       perguntar_e_ajustar(CaloriasFinal, NovosPools)
    ;  nl, write('Certo! Dieta semanal finalizada. Bom apetite!'), nl
    ).

remover_de_pools(Pools, Remover, NovosPools) :-
    findall(Refeicao-Categoria-NovaLista,
        ( member(Refeicao-Categoria-Lista, Pools),
          subtract(Lista, Remover, NovaLista)
        ),
        NovosPools).
