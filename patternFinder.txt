# Authors: Matheus Maia ; Gabriela Dellamora ; Luize Iensse

.text
	main:
		subi 		$sp, $sp, 4 	# Reserva espaÃƒÂ§o para 1 parÃƒÂ¢metros para funÃƒÂ§ÃƒÂ£o (1 * 4 bytes = 1 * 32 bits)
		# INICIALIZA TAMANHO E VETOR DE DADOS
		la 		$t1, vetorDados
		sw 		$t1, 0($sp)
		jal 		carregaVetor	
		
		lw		$t2, 0($sp) 	# $t2 = tamVetorDados
		
		
		# INICIALIZA TAMANHO E VETOR DE PADRAO
		la 		$t3, vetorPadrao
		sw 		$t3, 0($sp)	# $t3 = vetorPadrao
		jal 		carregaVetor
		
		lw		$t4, 0($sp)	# $t4 = tamVetorPadrao		
		
		# INICIALIZA PROCEDIMENTO DE CONTAGEM DE PADRAO
		addi		$sp, $sp, 4 	#NORMALIZEI O STACK POINTER
		
		li		$t5, 0		# $t5 = contabilizaPadrao
		li 		$t6, 0		# $t6 = posicaoDados
		
		# ENQUANTO (posicaoDados + tamVetorPadrao) <= tamVetorDados, faÃƒÂ§o o loop
		loopVerificaPadrao:
			# vou fazer chamada de funÃƒÂ§ÃƒÂ£o, preciso liberar espaÃƒÂ§o para receber parÃƒÂ¢metros pela stack. Nesse caso, 5 parÃƒÂ¢metros.
			subi 		$sp, $sp, 20	# ( 5 * 4 bytes = 5 * 32 bits)
			sw 		$t1, 0($sp)		# arg1: * vetorDados
    			sw 		$t6, 4($sp)		# arg2: posicaoDados
    			sw 		$t3, 8($sp)		# arg3: * vetorPadrao
			li 		$s0, 0			# PosPadrao
    			sw 		$s0, 12($sp)		# arg4: posicaoPadrao
    			sw 		$t4, 16($sp)		# arg5: tamvetorPadrao

			jal 		encontraPadrao
			
			lw		$v0, 0($sp)		#v0 = encontraPadrao(....)
			addu		$t5, $t5, $v0		# contabilizaPadrao += encontraPadrao(...)
			
			addiu 		$t6, $t6, 1		# posicaoDados++
			
			addu 		$t0, $t6, $t4 				# $t0 = posicaoDados + tamVetorPadrao			
			ble  		$t0, $t2, loopVerificaPadrao		# se posicaoDados <= tamVetorDados, fico no loop
			
			#SENÃO, FINALIZO MAIN	
			
			# Printa String
			li 			$v0, 4
			la 			$a0, imprimeQuantPadroesContabilizados
			syscall
		
			# Printa Inteiro
			li 			$v0, 1
			addu 			$a0, $zero, $t5
			syscall
			
			# Termina Programa
			li 			$v0, 10
			syscall
	
	carregaVetor:
		# LEITURA DE PARÂMETRO
   		lw 			$s1, 0($sp)		# endereÃ§o do vetorDados
    		addi 			$sp, $sp, 4  		# NORMALIZO STACK POINTER
    
   	 	# PRINT TEXTO
  	 	li 			$v0, 4
  	 	la 			$a0, mensagemTamanho
   	 	syscall

    	 	# $V0 = INPUT(TAM_DO_VETOR)
   	 	li 			$v0, 5
         	syscall
         
         	# ENVIANDO TAM_DO_VETOR PARA STACK
         	subi 			$sp, $sp, 4		# Libera espaÃ§o para 1 item na pilha
   	 	sw 			$v0, 0($sp) 		# Salva a entrada na pilha
   	 	
   	 	subi 			$t9, $v0, 1 	# $t9 = indice = quantidade - 1 (pq comeca em 0)

		# multiplica por 4 (qtd de bytes)
		li 			$s7, 4		
		mul 			$t9, $t9, $s7 		# $t9 = tamanho do vetor em bytes
		
		
		# CARREGA VETOR DE DADOS
		addu			$t8, $s1, $t9		# $t8 = endereço_do_vetor + tam_do_vetor_em_bytes
		loopCarregaVetor:
			#compara se o tamanho do vetor e igual a zero
   			blt 			$t8, $s1, endLoopCarregaVetor 
   			
    			# PRINT TEXTO
    			li 			$v0, 4
   			la			$a0, mensagemValor
   			syscall

    			# $v0 = input("Qual o valor do elemento?")
   			li 			$v0, 5			# v0 = input(valor)
    			syscall
    			sw 			$v0, 0($t8)  		# vetorDados[ultimo_indice_do_vetor] = v0

    			# Incrementa a posicao para continuar a proxima iteracao do loop
    			subi 			$t8, $t8, 4		# passo para vetorDados[ultimo_indice_do_vetor-1]
    			j  			loopCarregaVetor 	#aqui volta para o loop 

	endLoopCarregaVetor:
    			jr			$ra
    	
    	encontraPadrao:
    			lw 			$s0, 16($sp)		# arg5: $s0 = tamvetorPadrao
    			lw 			$s1, 12($sp)		# arg4: $s1 = posicaoPadrao
    			lw 			$s2, 8($sp)		# arg3: $s2 = end vetorPadrao
    			lw 			$s3, 4($sp)		# arg2: $s3 = posicaoDados
    			lw 			$s4, 0($sp)		# arg1: $s4 = end vetorDados
    			addi			$sp, $sp, 20		# NORMALIZEI A PILHA
    			
    			# Multiplica o posicaoDados e posicaoPadrao por 4
    			mul			$s7, $s3, 4		#PosicaoDados
    			mul 			$t8, $s1, 4		#PosicaoPadrao
    			
    			addu			$s6, $s4, $s7		# vetDados[posDados]
    			lw 			$a0, 0($s6)		# $a0 = vetorDados[posDados]
    			
    			addu 			$s6, $s2, $t8		# vetPadrao[posPadra]
    			lw			$a1, 0($s6)		# $a1 = vetorPadrao[posPadrao]
    			
    			bne			$a0, $a1, retornaZero
    			
    			subi 			$v0, $s0, 1		#posPadrao == tamPadrao-1
    			beq 			$s1, $v0, retornaUm
    			
    			
    			# SENÃO, CHAMA RECURSÃO
    			subi 			$sp, $sp, 20	# ( 5 * 4 bytes = 5 * 32 bits)
			sw 			$s4, 0($sp)		# arg1: * vetorDados
			addi 			$s3, $s3, 1		# PosicaoDados++
    			sw 			$s3, 4($sp)		# arg2: posicaoDados + 1
    			sw 			$s2, 8($sp)		# arg3: * vetorPadrao
    			addi 			$s1, $s1, 1		# PosicaoPadrao++
    			sw 			$s1, 12($sp)		# arg4: posicaoPadrao + 1
    			sw 			$s0, 16($sp)		# arg5: tamvetorPadrao

    			j 			encontraPadrao
    				
    		retornaZero:
    			subi 			$sp, $sp, 4
    			li 			$v0, 0
    			sw			$v0, 0($sp)
    			jr			$ra
    			
    		retornaUm:
    			subi 			$sp, $sp, 4
    			li 			$v0, 1
    			sw			$v0, 0($sp)
    			jr			$ra

    	
    	
    	# Params: int * vetDados, int posDados, int * vetPadrao, int posPadrao, int tamPadrao
	# FunÃ§Ã£o recursiva! Muito cuidado com o PC e o IR
	# TODO: soma do $sp += 4 dentro da funÃ§Ã£o, assim ela fica dinÃ¢mica e evita problema de esquecer de somar fora da chamada de funcao.
	# TODO: dentro da funÃ§Ã£o, preciso enviar para a stack 1 output
	# Envia Ã  stack (OUTPUT): 0 ou 1, encontrei padrÃ£o ou nÃ£o encontrei padrÃ£o. Dentro da funÃ§Ã£o fazemos uma soma recursiva para retornar o tamanho total de contabilizaPadrao
	
.data
	imprimeQuantPadroesContabilizados: .asciiz "Quantidade de Padroes contabilizados: "				# Foram retirados sinais graficos para evitar problemas na impressao
	mensagemTamanho: .asciiz "Informe o numero de dados a serem inseridos no vetor: "
	mensagemValor: .asciiz "Informe um dado a ser inserido no vetorDados: "
	
	vetorPadrao: .word 0, 0, 0, 0, 0										# MAX: 5 palavras
	vetorDados: .space 200												# MAX: 50 palavras (50 * 32 bits = 50 * 4 bytes) 
	

# AnotaÃƒÂ§ÃƒÂµes importantes:
	# - A stack ÃƒÂ© FILO, portanto se vocÃƒÂª envia pra stack os seguintes parÃƒÂ¢metros nesta ordem -> 5, 4, 3, 2, 1 ; VocÃƒÂª vai retirar os parÃƒÂ¢metros na ordem -> 1, 2, 3, 4, 5
	# - ÃƒÂ‰ possÃƒÂ­vel bypassar a regra acima regra manipulando o $sp, mas eu nÃƒÂ£o recomendo pra nÃƒÂ£o complicar a mais o projeto.
	# Fiz um dicionÃƒÂ¡rio de registradores pra facilitar a manipulaÃƒÂ§ÃƒÂ£o. estÃƒÂ¡ no README.MD
	
