# 英汉词典C语言
```c
/**
题目：英汉字典之(初级版)
**/
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
///3个常量
#define DICTIONARY_SIZE 100
#define WORD_SIZE 50
#define MEANING_SIZE 200*2
typedef struct {
    char word[WORD_SIZE +1];///单词
    char meaning[MEANING_SIZE +1];///中文解释
} Entry;///类型 Entry 词条
Entry dictionary[DICTIONARY_SIZE];///字典
int entryNum = 0;///词条数

void addWord(char line[]);///单词添加函数
char* lookup(char word[]);///查询过程函数(最后返回对应解释的地址)
///void listDictionary();///查询结果输出函数(做调试用) , 最后应该删除
int main()
{
    ///freopen("out.txt" , "w" , stdout);
    int n;
    scanf("%d", &n); ///1.输入词条数 n
    char s[20];
    gets(s);///读取n输入之后的空格
    // 我猜这个是单词加释义
    // line也是一行的意思
    char line[WORD_SIZE + MEANING_SIZE +1 +1];
    ///2.输入n个词条，并逐一加入字典dictionary
    while(n--){
        gets(line);///2.1 输入词条line
        addWord(line);///2.2 把line加入字典dictionary
        ///listDictionary();
    }
    int k;
    scanf("%d", &k);///3.输入要查询的单词数目 k
    gets(s);
    char word[WORD_SIZE +1];///长度暂时取英文单词长度
    char* pMeaning;///输出指针，用来输出对应的中文解释
    ///4.输入k个单词，并逐一查询并输出
    while(k--){
        gets(word);///4.1 输入单词word
        pMeaning = lookup(word);///4.2 在dictionary查询word，查询结果pMeaning
        ///4.3 如果pMeaning == NULL，
        ///    输出"Sorry, not in this dictionary!"
        ///    否则，输出pMeaning
        if(pMeaning == NULL){
            printf("Sorry, not in this dictionary!\n");
        }else{
            printf("%s\n", pMeaning);
        }
    }
    ///fclose (stdout);
    return 0;
}
void addWord(char line[]){
    ///1.分割line成word和meaning两部分
    char *p;
    p = strchr(line, ' ');///1.1 找line中第一个空格的下标i
    *p = '\0';///1.2 把line[i] 改为0
    char * pWord = line;///1.3 用指针pWord和pMeaning分别指向word和meaning两部分
    char * pMeaning = p + 1;
    ///2.把word和meaning添加到dictionary中
    strcpy(dictionary[entryNum].word, pWord);
    strcpy(dictionary[entryNum].meaning, pMeaning);
    entryNum ++;
}
char* lookup(char word[]){
    char *p;
    int i;
    for(i = 0; i < entryNum; i++){
        if(strcmp(dictionary[i].word,word)== 0){
            break;
        }
    }
    if(i < entryNum){
        p = dictionary[i].meaning;
    }else{
        p = NULL;
    }

    return p;
}
/**void listDictionary(){
    int i;
    for(i = 0; i < entryNum; i++){
        printf("%s%s\n", dictionary[i].word, dictionary[i].meaning);
    }
}
**/

```
