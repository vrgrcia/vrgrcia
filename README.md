#include <stdio.h>
#include <stdlib.h>
#include <time.h>

#define TAMANHO 10
#define PORTA_AVIOES 5
#define NAVIO_TANQUE 4
#define CONTRATORPEDEIRO 3
#define SUBMARINO 2

#define AGUA '~'
#define NAVIO 'N'
#define ACERTO 'A'
#define ERRO 'E'

void imprimirTabuleiro(char tabuleiro[TAMANHO][TAMANHO], int jogador)
{
    printf("Tabuleiro do Jogador %d:\n", jogador);
    printf("   ");
    for (int i = 0; i < TAMANHO; i++)
    {
        printf("%d  ", i + 1);
    }
    printf("\n");

    for (int i = 0; i < TAMANHO; i++)
    {
        printf("%c  ", 'A' + i);
        for (int j = 0; j < TAMANHO; j++)
        {
            printf("%c  ", tabuleiro[i][j]);
        }
        printf("\n");
    }
}

void inicializarTabuleiro(char tabuleiro[TAMANHO][TAMANHO])
{
    for (int i = 0; i < TAMANHO; i++)
    {
        for (int j = 0; j < TAMANHO; j++)
        {
            tabuleiro[i][j] = AGUA;
        }
    }
}

int posicionarNavio(char tabuleiro[TAMANHO][TAMANHO], char tipo, int tamanho)
{
    int x, y, direcao;
    do
    {
        x = rand() % TAMANHO;
        y = rand() % TAMANHO;
        direcao = rand() % 2; // 0 para horizontal, 1 para vertical
    } while (!podeColocarNavio(tabuleiro, x, y, direcao, tamanho));

    if (direcao == 0)
    {
        for (int i = y; i < y + tamanho; i++)
        {
            tabuleiro[x][i] = tipo;
        }
    }
    else
    {
        for (int i = x; i < x + tamanho; i++)
        {
            tabuleiro[i][y] = tipo;
        }
    }

    return 1;
}

int podeColocarNavio(char tabuleiro[TAMANHO][TAMANHO], int x, int y, int direcao, int tamanho)
{
    if (direcao == 0)
    {
        if (y + tamanho > TAMANHO)
            return 0;

        for (int i = y; i < y + tamanho; i++)
        {
            if (tabuleiro[x][i] != AGUA)
                return 0;
        }
    }
    else
    {
        if (x + tamanho > TAMANHO)
            return 0;

        for (int i = x; i < x + tamanho; i++)
        {
            if (tabuleiro[i][y] != AGUA)
                return 0;
        }
    }
    return 1;
}

int ataqueJogador(char tabuleiro[TAMANHO][TAMANHO], int jogador)
{
    char linha;
    int coluna;

    printf("Jogador %d, é sua vez de atacar!\n", jogador);
    printf("Informe a coordenada (ex: A1): ");
    scanf(" %c%d", &linha, &coluna);

    if (linha >= 'A' && linha <= 'J' && coluna >= 1 && coluna <= 10)
    {
        int x = linha - 'A';
        int y = coluna - 1;

        if (tabuleiro[x][y] == NAVIO)
        {
            printf("Você acertou um navio!\n");
            tabuleiro[x][y] = ACERTO;
            return 1;
        }
        else if (tabuleiro[x][y] == AGUA)
        {
            printf("Você errou o tiro!\n");
            tabuleiro[x][y] = ERRO;
        }
        else
        {
            printf("Você já atacou essa posição!\n");
        }
    }
    else
    {
        printf("Coordenada inválida. Tente novamente.\n");
    }

    return 0;
}

int verificaVitoria(char tabuleiro[TAMANHO][TAMANHO])
{
    int naviosPortaAvioes = 0;
    int naviosTanque = 0;

    for (int i = 0; i < TAMANHO; i++)
    {
        for (int j = 0; j < TAMANHO; j++)
        {
            if (tabuleiro[i][j] == NAVIO)
            {
                if (i + PORTA_AVIOES <= TAMANHO)
                {
                    int count = 0;
                    for (int k = i; k < i + PORTA_AVIOES; k++)
                    {
                        if (tabuleiro[k][j] == NAVIO)
                            count++;
                    }
                    if (count == PORTA_AVIOES)
                        naviosPortaAvioes++;
                }

                if (j + NAVIO_TANQUE <= TAMANHO)
                {
                    int count = 0;
                    for (int k = j; k < j + NAVIO_TANQUE; k++)
                    {
                        if (tabuleiro[i][k] == NAVIO)
                            count++;
                    }
                    if (count == NAVIO_TANQUE)
                        naviosTanque++;
                }
            }
        }
    }

    if (naviosPortaAvioes == 0 && naviosTanque == 0)
        return 1; // Todos os NAVIO_TANQUE e PORTA_AVIOES foram eliminados
    else
        return 0; // Ainda há navios no tabuleiro
}

int ataqueComputador(char tabuleiro[TAMANHO][TAMANHO], int jogador)
{
    int x, y;

    do
    {
        x = rand() % TAMANHO;
        y = rand() % TAMANHO;
    } while (tabuleiro[x][y] == ACERTO || tabuleiro[x][y] == ERRO);

    if (tabuleiro[x][y] == NAVIO)
    {
        printf("O Computador %d acertou em %c%d!\n", jogador, 'A' + x, y + 1);
        tabuleiro[x][y] = ACERTO;
        return 1;
    }
    else
    {
        printf("O Computador %d errou em %c%d!\n", jogador, 'A' + x, y + 1);
        tabuleiro[x][y] = ERRO;
        return 0;
    }
}

int main()
{
    char tabuleiroJogador1[TAMANHO][TAMANHO];
    char tabuleiroJogador2[TAMANHO][TAMANHO];

    srand(time(NULL));

    inicializarTabuleiro(tabuleiroJogador1);
    inicializarTabuleiro(tabuleiroJogador2);

    // Posicione os navios aleatoriamente para os jogadores
    posicionarNavio(tabuleiroJogador1, 'P', PORTA_AVIOES);
    for (int i = 0; i < 2; i++)
    {
        posicionarNavio(tabuleiroJogador1, 'T', NAVIO_TANQUE);
        posicionarNavio(tabuleiroJogador1, 'C', CONTRATORPEDEIRO);
        posicionarNavio(tabuleiroJogador1, 'S', SUBMARINO);
    }

    posicionarNavio(tabuleiroJogador2, 'P', PORTA_AVIOES);
    for (int i = 0; i < 2; i++)
    {
        posicionarNavio(tabuleiroJogador2, 'T', NAVIO_TANQUE);
        posicionarNavio(tabuleiroJogador2, 'C', CONTRATORPEDEIRO);
        posicionarNavio(tabuleiroJogador2, 'S', SUBMARINO);
    }

    int vez = 1; // jogador 1 começa

    while (1)
    {
        if (vez == 1)
        {
            imprimirTabuleiro(tabuleiroJogador1, 1);
            if (ataqueJogador(tabuleiroJogador2, 1))
            {
                printf("Você acertou um navio! Continue jogando...\n");
            }
            else
            {
                vez = 2;
            }
        }
        else
        {
            imprimirTabuleiro(tabuleiroJogador2, 2);
            if (ataqueComputador(tabuleiroJogador1, 2))
            {
                printf("O Computador 2 acertou um navio! Continue jogando...\n");
            }
            else
            {
                vez = 1;
            }
        }

        if (verificaVitoria(tabuleiroJogador1))
        {
            printf("Jogador 2 venceu!\n");
            break;
        }
        else if (verificaVitoria(tabuleiroJogador2))
        {
            printf("Jogador 1 venceu!\n");
            break;
        }
    }

    return 0;
}
