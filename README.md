# 9.9-
#include<stdlib.h>
#include<string>
#include<iostream>
#include<ctime>
#include<windows.h>
#include<conio.h>
using namespace std;
static int I = 0;//猪圈序号
static int i = 0;//猪的序号
static string a = "黑猪";
static string b = "小花猪";
static string c = "大花白猪";
static string d = "空";
static double price = 0;
static int soldpignum=0;

typedef struct Pig{//猪
    int pigstynum;
    int num;
    char *type;
    double weight;
    double time;
    double pri;
}pig;
static Pig soldpig[1000];

typedef struct Pigsty{//猪圈
    int no;
    int pignum;
    int black;
    struct node *a;
}pigsty;

typedef struct Node//猪圈指针
{
    pigsty Data;
    struct Node *next;
}Lnode;

typedef struct node//猪指针
{
    pig data;
    struct node *next;
}lnode;

void Clean(Lnode *p){//将猪圈清空
    Lnode *r = p;
    r->Data.black = 0;
    r->Data.no = 0;
    r->Data.pignum = 0;
}

void clean(lnode *p){//将猪槽清空
    lnode *r = p;
    r->data.num = 0;
    r->data.pigstynum = 0;
    r->data.time = 0;
    r->data.type = &d[0];
    r->data.weight = 0;
}

void creat(lnode *hh,int n)//生成猪槽
{
    lnode *p,*r;
    int j;
    r=hh;
    for(j=0;j<n;j++)
    {
        p=(lnode *)malloc(sizeof(lnode));
        r->next=p;
        clean(r);
        r->data.pigstynum = I;
        r->data.num = i;
        i++;
        if(i==10) i = i - 10;
        r=p;
    }
    I++;
    r->next=NULL;
}

void Creat(Lnode *b,int n)//生成猪圈
{
    Lnode *p,*r;
    int j;
    r=b;
    for(j=0;j<n;j++)
    {
        p=(Lnode *)malloc(sizeof(Lnode));
        r->next=p;
        Clean(r);
        r->Data.no = I;
        lnode *hh;
        hh=(lnode *)malloc(sizeof(lnode));
        hh->next=NULL;
        r->Data.a = hh;
        creat(hh,10);
        r=p;
    }
    r->next=NULL;
}

void Input(Lnode *p,pig u)//放1猪进猪圈
{
    for (int j = 0; j < 10;j++){
        Lnode *r = p;
        for (; r->next != NULL; r = r->next)
        {
            if (r->Data.pignum <= j){
                if (r->Data.pignum==0){
                    if (u.type==&a[0]) r->Data.black = 1;
                    lnode *h = r->Data.a;
                    for (; h->next != NULL; h = h->next)
                    {
                        if(h->data.weight == 0)
                        {
                        h->data.weight = u.weight;
                        h->data.type = u.type;
                        h->data.pigstynum = r->Data.no;
                        h->data.time = u.time;
                        r->Data.pignum++;
                        return;
                        }
                    }
                }
                if(((r->Data.pignum!=0&&r->Data.black==0)&&(u.type==&b[0]||u.type==&c[0]))||(r->Data.black==1&&u.type==&a[0])){
                    lnode *h = r->Data.a;
                    for (; h->next != NULL; h = h->next)
                    {
                        if(h->data.weight == 0)
                        {
                        h->data.weight = u.weight;
                        h->data.type = u.type;
                        h->data.pigstynum = r->Data.no;
                        h->data.time = u.time;
                        r->Data.pignum++;
                        return;
                        }
                    }
                }      
            }
        }
    }
}

void output(Lnode *h)//输出
{
    Lnode *p;
    p = h;
    while(p->next!=NULL)
    {
        cout<<p->Data.no<<endl;
        lnode *r = p->Data.a;
        while(r->next!=NULL){
            if(r->data.weight!=0)
            cout << r->data.num << r->data.type << r->data.weight <<"kg"<<r->data.time<< "天 ";
            r=r->next;
        }
        cout << endl;
        p=p->next;
    }
}

void ergoPW(Lnode *h){//遍历并增加每头猪1天的体重
    int t = 1000;
    Lnode *p;
    p = h;
    while (p->next != NULL)
    {
        lnode *r = p->Data.a;
        for (;r->next != NULL ; r = r->next)
        {
            if(r->data.weight!=0){
                double x;
                x = double((rand() % 12) / 10.00);
                r->data.weight = r->data.weight + x;
                r->data.time++;
            }  
        }
        p = p->next;
    }
    Sleep(t);     
}

void onlyergo(Lnode *h,int n){//找到对应猪圈并输出这个猪圈的猪情况
    Lnode *p = h;
    while (p->Data.no!=n){
        p = p->next;
    }
    lnode *r = p->Data.a;
    while(r->next!=NULL)
    {
        if(r->data.weight!=0)
        cout << r->data.num << r->data.type << r->data.weight << "kg" << r->data.time << "天 ";
        r = r->next;
    }
    cout << endl;
}


void pricesum(Lnode *h){//计算所有出圈猪的价格并清空出圈猪槽
    Lnode *p;
    p = h;
    while (p->next != NULL)
    {
        lnode *r = p->Data.a;
        int j = 0;
        for (;r->next != NULL; r = r->next)
        {
            if(r->data.time>=365||r->data.weight>75.00){
                soldpig[soldpignum].time = r->data.time;
                soldpig[soldpignum].type = r->data.type;
                soldpig[soldpignum].weight = r->data.weight;
                soldpig[soldpignum].pigstynum = r->data.pigstynum;
                soldpig[soldpignum].num = r->data.num;
                if (r->data.type == &a[0]){
                    price = price + r->data.weight * 30;
                    soldpig[soldpignum].pri = r->data.weight * 30;
                }
                if (r->data.type == &b[0]){
                    price = price + r->data.weight * 14;
                    soldpig[soldpignum].pri = r->data.weight * 14;
                }   
                if (r->data.type == &c[0]){
                    price = price + r->data.weight * 12;
                    soldpig[soldpignum].pri = r->data.weight * 12;
                }
                soldpignum++;    
                r->data.time = 0;
                r->data.type = &d[0];
                r->data.weight = 0;
                j++;
            }
        }
        p->Data.pignum = p->Data.pignum - j;
        if(p->Data.pignum==0) p->Data.black = 0;
        p = p->next;
    }
}

bool Full(Lnode *h){//看看猪圈满了没有
    Lnode *p;
    p = h;
    while(p->next!=NULL)
    {
        lnode *r = p->Data.a;
        while(r->next!=NULL){
            if(r->data.weight==0) return false;
            r=r->next;
        }
        p=p->next;
    }
    return true;
}

void random(pig *u){//随机一个猪
    double wei;
    wei=rand() % 30+20.00;
    u->weight = wei;
    int tpnum;
    tpnum = rand() % 3;
    switch(tpnum){
        case 0:
            u->type = &a[0];
            break;
        case 1:
            u->type = &b[0];
            break;
        case 2:
            u->type = &c[0];
            break;
    };
    u->time = 0;
}

int main()
{    
    int button;
    cout << "               养猪模拟" << endl;
    cout << "======================================" << endl;
    cout << "             1.开始游戏" << endl;
    cout << "             2.游戏规则" << endl;
    cout << "             3.退出游戏" << endl;
    cout << "======================================" << endl;
    cin >> button;
    if(button==2){
        cout << "         1.一共有100个猪圈,每个猪圈最多养10头猪" << endl;
        cout << "         2.返回游戏" << endl;
        cin >> button;
    }
    if(button==3) return 0;
    Lnode *p;
    p=(Lnode *)malloc(sizeof(Lnode));
    p->next = NULL;
    Creat(p,100);
    srand((int)time(0));
    int n = rand() % 100;
    while(n==0){
        n = rand() % 100;
    }
    pig u;
    for (int j = 0; j < n;j++){
        random(&u);
        Input(p, u);
    }
    cout << "养猪中。。。"<<endl;
    cout << "按任意键可查询猪圈情况" << endl;
    int days = 1;
    while (1)
    {   ergoPW(p);
        if (days % 90 == 0)
        {
            pricesum(p);
            int littlepig = rand() % 100;
            for (int jj = 0; jj < littlepig; jj++)
            {
                if(Full(p)) break;
                random(&u);
                Input(p, u);
            }
        }
        days++;
        if (_kbhit()){//如果有按键按下，则_kbhit()函数返回真，头文件conio.h
            getch();//键盘输入，不会显示
            cout << "选项：" << endl;
            cout << "1.查看当前全部猪圈" << endl;
            cout << "2.只看当前一个猪圈" << endl;
            cout << "3.查看卖掉的猪" << endl;
            cout << "4.继续养猪" << endl;
            cout << "5.退出游戏" << endl;
            cin >> button;
            if (button == 1){
                cout << "这是第"<<days<<"天的所有猪圈情况：" << endl;
                output(p);
            }
            if (button == 2){
                cout << "选择猪圈编号：0-99" << endl;
                cin >> button;
                cout << "这是第" << days << "天的第" << button << "个猪圈的情况：" << endl;
                onlyergo(p, button);
            }
            if (button == 3){
                cout << "卖猪情况：" << endl;
                for (int jj = 0; jj < 100;jj++){
                    int wave = 1;
                    cout << jj << endl;
                    cout << "第" << wave << "波" << endl;
                    for (int rr = 0; soldpig[rr].weight != 0;rr++){
                        if (soldpig[rr].pigstynum == jj){
                            if(soldpig[rr].pigstynum!=soldpig[rr+1].pigstynum){
                                wave++;
                                cout << "第" << wave << "波" << endl;
                            } 
                            cout<< soldpig[rr].num <<"号"<< soldpig[rr].type << soldpig[rr].weight <<"kg"<< soldpig[rr].time << "天 卖了"<<soldpig[rr].pri<<"元"<<endl;
                        }
                    }   
                }
                cout << endl;
                cout << "一共卖出：" << price << "元" << endl;
            }
            if(button==5) break;
            cout << "按任意键可继续养猪" << endl;
            getch();
            cout << "养猪中。。。" << endl;
        } 
    }
    return 0; 
}
