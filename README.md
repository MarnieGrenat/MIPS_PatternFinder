# PatternFinder_vector_MIPS;
Código assembly MIPS para encontrar padrões dentro um vetor de números inteiros. Trabalho de Fundamento de Sistemas Computacionais, 2023
#### Authors: [Gabriela Dellamora](https://github.com/MarnieGrenat), [Matheus Maia](https://github.com/mathemaia), [Luize Iensse](https://github.com/Lienss) 


### Dicionário de Registradores:
- $t0 = registrador para queimar. Como se fosse uma variável auxiliar.
  - Dentro do loopVerificaPadrao, ela não pode ser alterada, pois dentro dentro do while ela vai ser sempre sobrescrita, perdendo o valor recentemente atribuído;
- $t1 = vetor de Dados
- $t2 = tamanho do vetor de Dados
- $t3 = vetor de padrão
- $t4 = tamanho do vetor de padrão
- $t5 = contabilizador de padrão, recebe o número total de padrões dentro do vetor
- $t6 = posição (índice) que estamos verificando atualmente dentro do while.
- $t7 = registrador auxiliar para receber o retorno da função encontraPadrao()

### Parâmetros e retorno das funções

- encontraPadrao(int * vetDados, int posDados, int * vetPadrao, int posPadrao, int tamPadrao)
  - Parâmetros:
    - vetor Dados ($t1)
    - posicaoDados ($t6)
    - vetor Padrao ($t3)
    - posicaoPadrao (É algo manipulado dentro da função recursiva, na chamada da main deve receber $zero)
    - tamanho do Vetor Padrao ($t2)
  - Retorno:
    - Quantidade de padrões achados.

- carregaVetor(int * enderecoVetor)
   - Parâmetro:
     - endereço do vetor.
   - Retorno:
     - tamanho do vetor.
