#include <time.h>
#include <stdio.h>
#include <conio.h>
#include <stdlib.h>
#include <windows.h>

#define FIELD_WIDTH  9                       //フィールドの横幅
#define FIELD_HEIGHT 9                       //フィールドの縦幅

#define BOMB_MAX 25                          //マスにある爆弾の最大数
#define FIELD_MAX (FIELD_WIDTH*FIELD_HEIGHT) //マスの合計

#define None     0                           //なし
#define Mine     1                           //爆弾
#define Flag     2                           //旗
#define Open     3                           //開いたマス
#define Question 4                           //?マス

int cursorX, cursorY;                        //カーソルの座標用変数
int bomb;                                    //爆弾を配置する際にカウントするための変数
int count_bomb,count_flag;                   //mapにいくつ爆弾と旗があるかカウントするための変数
int count;                                   //動作切り替え用

int map[4][FIELD_HEIGHT][FIELD_WIDTH];       //[0]通常map用変数 [1]爆弾用変数 [2]デバッグ用変数 [3]マスの周囲の爆弾の数用の変数

int checkX[] = {0,-1,-1,0,1,1,1,0,-1};       //周囲を検出するための配列  X座標
int checkY[] = {0,0,-1,-1,-1,0,1,1,1};       //周囲を検出するための配列  Y座標

clock_t start, end;                          //時間計測用の変数
FILE* fp;                                    //デバッグ用のファイルポインタ

void Log(int n=0) {////////////////////////////////////デバッグ用////////////////////////////////////
	if (n == 1) {/************開始時点のmapをテキストファイルに保存************/
		fprintf(fp, "開始時点\n\n");
		for (int y = 0; y < FIELD_HEIGHT; y++) {
			for (int x = 0; x < FIELD_WIDTH; x++) {
				if (map[1][y][x] == Mine)
					fprintf(fp, " ★ ");
				else
					fprintf(fp, " ■ ");
			}
			fprintf(fp, "\n\n");
		}
	}

	if (n == 2) {/************終了時点のmapをテキストファイルに保存************/
		fprintf(fp, "\n\n終了時点\n\n");
		for (int y = 0; y < FIELD_HEIGHT; y++) {
			for (int x = 0; x < FIELD_WIDTH; x++) {
				if (map[2][y][x] == Flag)
					fprintf(fp, " ▲ ");
				else if (map[2][y][x] == Open && map[3][y][x] != 0)
					fprintf(fp, " %d  ", map[3][y][x]);
				else if (map[2][y][x] == Open && map[3][y][x] == 0)
					fprintf(fp, "    ");
				else if (map[1][y][x] == Mine)
					fprintf(fp, " ★ ");
				else if (map[2][y][x] == Question)
					fprintf(fp, " ？ ");
				else
					fprintf(fp, " ■ ");
			}
			fprintf(fp,"\n\n");
		}
	}
}

void Init() {////////////////////////////////////初期化////////////////////////////////////
	for (int y = 0; y < FIELD_HEIGHT; y++) {
		for (int x = 0; x < FIELD_WIDTH; x++) {
			for(int i=0;i<4;i++)
			map[i][y][x] = None;
		}
	}

	bomb = 0;
	start = clock();
	cursorX = (int)(FIELD_WIDTH / 2);
	cursorY = (int)(FIELD_HEIGHT / 2);
	count_flag = 0;
	count = 0;
}

void Screen() {////////////////////////////////////描画処理////////////////////////////////////
	system("cls");
	count_flag = 0;
	for (int y = 0; y < FIELD_HEIGHT; y++)
		for (int x = 0; x < FIELD_WIDTH; x++)
			if (map[0][y][x] == Flag)
				count_flag++;

	printf("\n爆弾:%d\n\n\n", bomb - count_flag);

	for (int y = 0; y < FIELD_HEIGHT; y++) {
		for (int x = 0; x < FIELD_WIDTH; x++) {
			if ((x == cursorX) && (y == cursorY))
				printf(" ◎ ");
			else if (map[0][y][x] == Flag)
				printf(" △ ");
			else if (map[0][y][x] == Open && map[3][y][x] != 0)
				printf(" %d  ", map[3][y][x]);
			else if (map[0][y][x] == Open && map[3][y][x] == 0)
				printf("    ");
			/*else if (map[1][y][x] == Mine)
				printf(" ☆ ");*/
			else if (map[0][y][x] == Question)
				printf(" ？ ");
			else
				printf(" ■ ");
		}
		puts("\n");
	}
}

int End() {////////////////////////////////////終了判定////////////////////////////////////
	int counter1=0; //爆弾と旗検出
	int counter2=0; //開いたマスをカウント
	for (int y = 0; y < FIELD_HEIGHT; y++) {
		for (int x = 0; x < FIELD_WIDTH; x++) {
			if (map[0][y][x] == Flag && map[1][y][x] == Mine)
				counter1++;
			if (map[0][y][x] != None)
				counter2++;
		}
	}
	if (counter1 == BOMB_MAX || counter2 == FIELD_MAX)
		return 1;
	else
		return 0;
}

void Checker() {////////////////////////////////////自動展開処理////////////////////////////////////
	for (int y = 0; y < FIELD_HEIGHT; y++) {
		for (int x = 0; x < FIELD_WIDTH; x++) {
			if (map[0][y][x] == Open && map[3][y][x] == 0) {
				for (int i = 1; i < 9; i++) {
					if (y + checkY[i] <= FIELD_HEIGHT - 1 && x + checkX[i] <= FIELD_WIDTH - 1 && x + checkX[i] >= 0 && y + checkY[i] >= 0)
						map[0][y + checkY[i]][x + checkX[i]] = Open;
				}
			}
		}
	}
}												

void System() {////////////////////////////////////システム////////////////////////////////////
	for (int y = 0; y < FIELD_HEIGHT; y++) {
		for (int x = 0; x < FIELD_WIDTH; x++) {
			map[2][y][x] = map[0][y][x];
		}
	}

	if (count == 0 && (cursorX > 0 && cursorX < FIELD_WIDTH - 1) && (cursorY > 0 && cursorY < FIELD_HEIGHT - 1)) {/************最初に開くマス************/
		for (int y = 0; y < 9; y++) {
			for (int x = 0; x < 9; x++)
				map[0][cursorY + checkY[y]][cursorX + checkX[x]] = Open;
		}
		count++;

	}
	else if (count != 0) {/************開けるかどうか & 爆弾があるかどうか***********/
		if (map[0][cursorY][cursorX] != Flag && map[1][cursorY][cursorX] != Mine && map[0][cursorY][cursorX] != Question)
			map[0][cursorY][cursorX] = Open;

		else if (map[0][cursorY][cursorX] == Flag)
			map[0][cursorY][cursorX] = Question;

		else if (map[0][cursorY][cursorX] == Question)
			map[0][cursorY][cursorX] = Flag;

		else if (map[1][cursorY][cursorX] == Mine) {
			system("cls");
			printf("\n\n\aそこは爆弾ですよ!w\n\n");
			Sleep(2000);
			printf("\n何かキーを押してください...[Esc]でリスタートできます");
			Sleep(500);
			if (_getch() == 0x1b)
				Init();
			else {
				Log(2);
				fclose(fp);
				exit(0);
			}
		}
	}
}

void Bomb() {////////////////////////////////////爆弾処理////////////////////////////////////
	System();
	if (count == 1) {/************爆弾配置************/
		for (int y = 0;y < FIELD_HEIGHT; y++) {
			for (int x = 0; x < FIELD_WIDTH; x++) {
				if ((rand() % BOMB_MAX/5 + 1) == 1 && bomb < BOMB_MAX && map[0][y][x] != Open && map[0][y][x] != Mine) {
					map[0][y][x] = Mine;
					bomb++;
				}
			}
			if (bomb < BOMB_MAX && y == 8) {
				y = 0;
				continue;
			}
		}

		for (int y = 0; y < FIELD_HEIGHT; y++) {/************爆弾検出************/
			for (int x = 0; x < FIELD_WIDTH; x++) {
				map[1][y][x] = map[0][y][x];
				count_bomb = 0;
				for (int i = 1; i < 9; i++) {
					if (map[0][y + checkY[i]][x + checkX[i]] == Mine)
						if(y + checkY[i] >= 0 && x + checkX[i] >= 0 && x + checkX[i] < FIELD_WIDTH && y + checkY[i] < FIELD_HEIGHT)
							count_bomb++;
				}
				map[3][y][x] = count_bomb;
			}
		}
		Log(1);
		count++;
	}
	for (int i = 0; i < 10; i++)
		Checker();

	Screen();
}

void Operatin() {////////////////////////////////////操作処理////////////////////////////////////
	switch (_getch()) {
		case 'w' : if (cursorY != 0)cursorY--; break;
		case 's' : if (cursorY != FIELD_HEIGHT - 1)cursorY++; break;
		case 'a' : if (cursorX != 0)cursorX--; break;
		case 'd' : if (cursorX != FIELD_WIDTH - 1)cursorX++; break;
		case ' ':  if (map[0][cursorY][cursorX] != Open && count_flag < BOMB_MAX && count != 0)map[0][cursorY][cursorX] == Flag ? map[0][cursorY][cursorX] = None : map[0][cursorY][cursorX] = Flag; break;
		case '\r': Bomb();break;
		case 0x1b:Init();
	}
}

int main(void) {////////////////////////////////////メイン関数////////////////////////////////////
	srand(time(NULL));
	Init();
	fopen_s(&fp, "DEBUG.txt", "w");

	while (End() == 0) {
		Screen();
		Operatin();
	}

	end = clock();
	system("cls");
	printf("かかった時間%.1f", (float)(end - start)/ CLOCKS_PER_SEC);
	
	Sleep(2500);
	printf("\n何かキーを押してください...\n\n");
	_getch();
	System();
	Log(2);
	fclose(fp);
}
