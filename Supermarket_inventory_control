// Supermarket inventory contol
// Date : 05. 01. 2016

#include<stdio.h>		// 기본 헤더
#include<string.h>		// 문자관련 함수 헤더
#include<malloc.h>		// 동적 할당
#include <windows.h>	// system 헤더

typedef struct item_type
{
	char item_name[30];					// 물건 이름
	int  item_num;						// 물건 수량
	char item_receiving_date[30];		// 물건 입고 날짜
	char item_expiration_date[30];		// 물건 유통기한
} Item_type;		
typedef struct err_item
{
	char item_name[30];					// 물건 이름
	int  item_num;						// 물건 수량
	char item_err[30];					// 물건 문제
} Err_Item;	
typedef struct Order_Item
{
	char item_name[30];					// 물건 이름
	int  item_num;						// 물건 수량	
} Order_Item;	

Item_type * items_list;					// 물건 목록 생성.
Err_Item * err_items_list;				// 물건 문제 목록.
Order_Item * order_items_list;				// 발주 목록 생성.

int i_num = 0, err_num, order_num;					// 물품 갯수.
void *rece_item(Item_type *items_list, int n);											// 물건 입고
void *new_item(Item_type items_add, Item_type *items_list, int n, int i);				// 물건 입고시 새로운 물건일 경우
void add_item(Item_type items_add, Item_type *items_list, int n, int i, int j);			// 물건 입고시 기존 물건일 경우
void *sub_item(Item_type *items_list, int n);											// 물건 출고(물건 구매)
void *err_item(Err_Item *err_items_list, int n);										// 물건 문제(오출, 미출, 반품)
void inven_control(Item_type *items_list, int n, Err_Item * err_items_list, int err, Order_Item * order_items_list, int order);	// 물건 관리
void *add_list(Order_Item *order_items_list, int n);										// 물건 발주 리스트 작성
void creation_file(Item_type *items_list, int n, Err_Item * err_items_list, int err, Order_Item * order_items_list, int order);	// 파일 작성
void display();																			// 포스기 화면

void main()
{	
	display();
}

void display()
{
	int num;
	int i;
	FILE * fp = fopen("product.txt", "r");		
	FILE * fp_err = fopen("err_list.txt", "r");
	FILE * fp_order = fopen("order_list.txt", "r");

	// 물품 목록
	fscanf(fp, "%d", &i_num);	
	items_list = (Item_type *)malloc( sizeof(Item_type) * i_num );  // 구조체 동적할당.
	for ( i = 0; i < i_num; i++ )
		fscanf(fp, "%s %d", &items_list[i].item_name, &items_list[i].item_num);

	// 문제 목록
	fscanf(fp_err, "%d", &err_num);
	err_items_list = (Err_Item *)malloc( sizeof(Err_Item) * err_num );  // 구조체 동적할당.
	for ( i = 0; i < err_num; i++ )
		fscanf(fp_err, "%s %d %s", &err_items_list[i].item_name, &err_items_list[i].item_num, &err_items_list[i].item_err);

	// 발주 목록
	fscanf(fp_order, "d", &order_num);
	if( fp_order == NULL )	{
		fp_order = fopen("order_list.txt", "w");
		fprintf(fp_order, "%d\n", 0);
	}
	else{
		order_items_list = (Order_Item *)malloc( sizeof(Order_Item) * order_num );  // 구조체 동적할당.
		for ( i = 0; i < order_num; i++ )
			fscanf(fp_order, "%s %d", &order_items_list[i].item_name, &order_items_list[i].item_num );
	}

	while (1) {
		system("cls");
		printf("┏━━━━━━━━━━━━━━━━━┓\n");
		printf("┃       슈퍼마켓  재고  관리       ┃\n");
		printf("┣━━━━━━━━━━━━━━━━━┫\n");
		printf("┃  1. 물건 입고  ┃  2. 물건 출고  ┃\n");
		printf("┃  3. 물건 관리  ┃  4. 물건 발주  ┃\n");
		printf("┃  5. 물건 문제  ┃  6. 파일 출력  ┃\n");
		printf("┃  7. 종     료  ┃                ┃\n");
		printf("┗━━━━━━━━━━━━━━━━━┛\n\n");

		printf(" 위 목록의 번호를 입력하시오 : ");
		scanf("%d", &num);


		switch(num){
		case 1:
			items_list = (Item_type *)rece_item(items_list, i_num);
			break;
		case 2:
			items_list = (Item_type *)sub_item(items_list, i_num);
			break;
		case 3:
			inven_control(items_list, i_num, err_items_list, err_num, order_items_list, order_num);
			break;
		case 4:
			order_items_list = (Order_Item *)add_list(order_items_list, order_num);
			break;
		case 5:
			err_items_list = (Err_Item *)err_item(err_items_list, err_num);
			break;
		case 6:
			creation_file(items_list, i_num, err_items_list, err_num, order_items_list, order_num);
			break;
		case 7:
			return;
		default:
			break;
		}
		system("pause");
	}
	fclose(fp);
	fclose(fp_err);
	fclose(fp_order);
}

void *rece_item(Item_type *items_list, int n)		// 물건 입고
{
	char item[100];
	int i, j, k, p, number = 0, state = 0;
	char **same_item;	// 문자열 배열.
	FILE * fp_1;
	Item_type *items_add;


	printf("물건 입고 목록이 들어 있는 파일 이름을 입력하시오 : ");		// product
	scanf("%s", &item);
	fp_1 = fopen(item, "r");		

	fscanf(fp_1, "%d", &k);
	items_add = (Item_type *)malloc( sizeof(Item_type) * k );  // 구조체 동적할당.

	for ( i = 0; i < k; i++ )
		fscanf(fp_1, "%s %d", &items_add[i].item_name, &items_add[i].item_num);
	printf("[ 입고 물품 목록 ]\n");
	for ( i = 0; i < k; i++ )
		printf("%s %d\n", items_add[i].item_name, items_add[i].item_num);

	// 동일 목록 갯수 찾기
	for ( i = 0; i < k; i++ ) {			
		for ( j = 0; j < i_num; j++ ) {
			if ( strcmp(items_add[i].item_name, items_list[j].item_name) == 0 ) {
				number++;				
			}
		}
	}
	printf("%d 개의 동일 목록이 있습니다.\n", number);

	// 동일 목록 제외
	if( number > 0 ){
		same_item = (char **)malloc(sizeof(char)*number);
		for( i=0; i< k - number; i++ )
		{
			*(same_item+i) = (char *)malloc(sizeof(char)*30);
		}

		number = 0;
		for ( i = 0; i < k; i++ ) {
			for ( j = 0; j < i_num; j++) {
				if ( strcmp(items_add[i].item_name, items_list[j].item_name) == 0) {
					same_item[number] = items_add[i].item_name;
					printf("%s \n", same_item[number]);
					number++;
					add_item( items_add[i], items_list, i_num, i, j );
					break;
				}
			}			
		}
	}

	p = 1;
	number = number;
	for( i = 0; i < k; i++ ){
		state = 0;
		for( j = 0; j < number; j++ ){
			if (strcmp( items_add[i].item_name, same_item[j]) == 0 ){
				state++;
			}			
		}
		if( state == 0 ){
			items_list = (Item_type *)new_item( items_add[i], items_list, i_num, i );	//p 는 새로운 물품이 증가할 때마다 증가. 이를 통해 realloc을 수행.
			//p++;
		}
	}
	fclose(fp_1);
	return items_list;
}

void *new_item(Item_type items_add, Item_type *items_list, int n, int i)	// 물건 입고시 새로운 물건일 경우
{		
	items_list = (Item_type *)realloc( items_list, sizeof(Item_type) * ( n + 1 ) );  // 구조체 동적할당.

	printf("입고 물건은 %s 입니다.\n", items_add.item_name );
	printf("입고 물건의 수량은 %d 개 입니다.\n", items_add.item_num);

	strcpy( items_list[ n ].item_name, items_add.item_name );
	items_list[ n ].item_num = items_add.item_num;

	printf("입고가 완료되었습니다.\n\n");

	/*for (i = 0; i< n + 1; i++ )
	printf("%s %d\n", items_list[i].item_name, items_list[i].item_num );*/
	i_num = n + 1;		// 총 품목 수 조정 : 추가된 품목까지.
	return items_list;
}

void add_item(Item_type items_add, Item_type *items_list, int n, int i, int j)		// 물건 입고시 기존 물건일 경우
{
	/*
	코드 구현
	*/
}

void *sub_item(Item_type *items_list, int n)		// 물건 출고(물건 구매)
{
	char sub_name[30];
	int i, sub_item, results, state = 0;
	printf("출고 품목을 입력하시오 : ");
	scanf("%s", sub_name);

	for ( i = 0; i < n; i++ ) {
		if ( strcmp(items_list[i].item_name, sub_name) == 0) {
			state = 1;
			break;
		}
	}

	if (state == 0) {
		printf("%s는 출고 품목에 존재하지 않습니다.\n", items_list[i].item_name );
	}
	else if (state == 1) {
		if(  items_list[i].item_num == 0 )
			printf("출고 품목은 현재 0 개입니다.\n");
		else {
			printf("%s의 재고량은 %d 개 입니다.\n", sub_name, items_list[i].item_num);
			printf("출고 수량을 입력하시오 : ");
			scanf("%d", &sub_item);
			results = items_list[i].item_num - sub_item;
			if( results < 0 )
				printf("수량이 잘못 입력되었습니다.\n");
			items_list[i].item_num = results;
			printf("%s는 현재 %d 개가 있습니다.\n", sub_name, items_list[i].item_num );
		}
	}
	return items_list;
}
void *err_item(Err_Item * err_items_list, int n)		// 물건 문제(오출, 미출, 반품)
{
	/*
	코드 구현
	*/
	return err_items_list; 
}
void inven_control(Item_type *items_list, int n, Err_Item * err_items_list, int err, Order_Item * order_items_list, int order)	// 물건 관리
{
	/*
	코드 구현
	*/

}
void *add_list(Order_Item *order_items_list, int n)		// 물건 발주 리스트 작성
{
	/*
	코드 구현
	*/
	return order_items_list;
}

void creation_file(Item_type *items_list, int n, Err_Item * err_items_list, int err, Order_Item *order_items_list, int order)	// 파일 작성
{
	int i, input;
	FILE * fp_inven = fopen("output_product_list.txt", "r");
	FILE * fp_err_o = fopen("output_err_list.txt", "r");
	FILE * fp_order_o = fopen("output_order_list.txt", "r");
	if( fp_inven == NULL ){
		fp_inven = fopen("output_product_list.txt", "w");		
	}
	if( fp_err_o == NULL ){
		fp_err_o = fopen("output_err_list.txt", "w");		
	}
	if( fp_order_o == NULL ){
		fp_order_o = fopen("output_order_list.txt", "w");		
	}
	system("cls");
	printf("┏━━━━━━━━━━━━━━━━━━━━━━┓\n");
	printf("┃    파일 작성을 원하는 목록을 입력하시오    ┃\n");
	printf("┣━━━━━━━━━━━━━━━━━━━━━━┫\n");
	printf("┃  1. 재고 물건 목록  ┃  2. 문제 물건 목록  ┃\n");
	printf("┃  3. 발주 물건 목록  ┃  4. 나가기          ┃\n");
	printf("┗━━━━━━━━━━━━━━━━━━━━━━┛\n\n");
	printf(" 위 목록의 번호를 입력하시오 : ");
	scanf("%d", &input);
	if ( input == 1 ){
		fprintf( fp_inven, "%d\n", n );
		if( n == 0 ){
			printf("재고 물품 없음\n");
		}
		else{
			for ( i = 0; i< n; i++ ){
				fprintf(fp_inven, "%s %d\n", items_list[i].item_name, items_list[i].item_num);
			}
		}
		fclose(fp_inven);
	}
	
	if( input == 2 ){
		fprintf( fp_err_o, "%d\n", err );
		if( err == 0 ){
			printf("문제 물품 없음\n");
		}
		else{
			for ( i = 0; i< err; i++ ){
				fprintf(fp_err_o, "%s %d %s\n", err_items_list[i].item_name, err_items_list[i].item_num, err_items_list[i].item_err);
			}
		}
		fclose(fp_err_o);
	}
	
	if( input == 3 ){
		fprintf( fp_order_o, "%d\n", order );
		if( order == 0 ){
			printf("발주 물품 없음\n");
		}
		else{
			for ( i = 0; i < order; i++ ){
				fprintf(fp_order_o, "%s %d\n", order_items_list[i].item_name, order_items_list[i].item_num);
			}

		}
		fclose(fp_order_o);
	}	
	
}
