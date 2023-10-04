# Authors: Matheus Maia ; Gabriela Dellamora ; Luize Iensse

.text
.globl main:
		subi 	$sp, $sp, 8 											# Reserva espaço para 2 parâmetros para função (2 * 4 bytes = 2 * 32 bits)
		
		# INICIALIZA TAMANHO E VETOR DE DADOS
		la 	$t0, vetorDados
		lw 	$t1, 0(t0)											# $t1 = vetorDados
		
		jal 	carregaVetor	
		lw	$t2, 0($sp) 											# $t2 = tamVetorDados

		# INICIALIZA TAMANHO E VETOR DE PADRAO
		la 	$t0, vetorPadrao
		lw 	$t3, 0($t0)											# $t3 = vetorPadrao
		
		jal 	carregaVetor
		lw	$t4, 0($sp)											# $t4 = tamVetorPadrao		
		
		# INICIALIZA PROCEDIMENTO DE CONTAGEM DE PADRAO
		li	$t5, 0												# $t5 = contabilizaPadrao
		li 	$t6, 0												# $t6 = posicaoDados
		
		# ENQUANTO (posicaoDados + tamVetorPadrao) <= tamVetorDados, faço o loop
		loopVerificaPadrao:
			addu 	$t0, $t6, $t4 										# $t0 = posicaoDados + tamVetorPadrao
			bgt	$t0, $t2, terminaLoopVerificaPadrao							# se posicaoDados+tamVetorPadrao > tamVetorDados, saio do loop	
			
			# vou fazer chamada de função, preciso liberar espaço para receber parâmetros pela stack. Nesse caso, 5 parâmetros.
			subi 	$sp, $sp, 20										# ( 5 * 4 bytes = 5 * 32 bits)
			
			jal 	encontraPadrao
			lw	$t7, 0($sp)										#t7 = encontraPadrao(....)
			
			addiu	$t5, $t5, $t7										# contabilizaPadrao += encontraPadrao(...)
					
			j	loopVerificaPadrao
			
	terminaLoopVerificaPadrao:
			
		# Printa String
		li $v0, 4
		la $a0, imprimeQuantPadroesContabilizados
		syscall
		
		# Printa Inteiro
		li $v0, 1
		lw $a0,	$t7
		syscall
			
		# Termina Programa
		li $v0, 10
		syscall
		
	
	carregaVetor:													# Params: int enderecoVetor
	# TODO: soma do $sp += 4 dentro da função, assim ela fica dinâmica e evita problema de esquecer de somar fora da chamada de função.
	# TODO: dentro da função, preciso enviar para a stack 1 output
	# Envia à stack (OUTPUT): tamanhoVetor
	
	
	encontraPadrao:													# Params: int * vetDados, int posDados, int * vetPadrao, int posPadrao, int tamPadrao
	# Função recursiva! Muito cuidado com o PC e o IR
	# TODO: soma do $sp += 4 dentro da função, assim ela fica dinâmica e evita problema de esquecer de somar fora da chamada de funcao.
	# TODO: dentro da função, preciso enviar para a stack 1 output
	# Envia à stack (OUTPUT): 0 ou 1, encontrei padrão ou não encontrei padrão. Dentro da função fazemos uma soma recursiva para retornar o tamanho total de contabilizaPadrao
	
	
.data
	imprimeQuantPadroesContabilizados: .asciiz "Quantidade de Padroes contabilizados: "				# Foram retirados sinais graficos para evitar problemas na impressao
	
	
	vetorPadrao: .word 0, 0, 0, 0, 0										# MAX: 5 palavras
	vetorDados: .space 200												# MAX: 50 palavras (50 * 32 bits = 50 * 4 bytes) 
	
# Anotações importantes:
	# - A stack é FILO, portanto se você envia pra stack os seguintes parâmetros nesta ordem -> 5, 4, 3, 2, 1 ; Você vai retirar os parâmetros na ordem -> 1, 2, 3, 4, 5
	# - É possível bypassar a regra acima regra manipulando o $sp, mas eu não recomendo pra não complicar a mais o projeto.
	# Fiz um dicionário de registradores pra facilitar a manipulação. está no README.MD
	