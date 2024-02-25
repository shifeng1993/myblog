---
title: 【C学习随笔】C语言的字符串函数
categories: C/C++
date: 2017-03-20
tags:
  - C
---
# strlen()
strlen()函数用于统计字符串的长度。
参数：字符串数组，
以下为示例，输出为len = 15
```c
#include <stdio.h>
#include <string.h>

int main(void){
	char str[] = "woshiyigehaoren";
	int len = strlen(str);
	printf("len = %d \n",len);
	return 0;
}
```

# strcat()
strlen()函数用于拼接字符串。
参数：两个字符串数组，
以下为示例，输出为：str = woshiyigehaoren 

```c
#include <stdio.h>
#include <string.h>

int main(void){
	char str[80] = "woshiyige";
	char str1[] = "haoren";
	strcat(str,str1);
	printf("str = %s \n",str);
	return 0;
}
```

# strncat()
strncat()函数用于拼接字符串。
参数：（主字符串，附加字符串，长度），
以下为示例，输出为：str = woshiyigehaoren；

```c
#include <stdio.h>
#include <string.h>

int main(void){
	char str[80],str1[30];
	strcpy(str,  "woshiyige");
	strcpy(str1,  "haoren");
	strncat(str, str1, 110);
	printf("str = %s\n",str);
	return 0;
}
```

# strcmp()
strcmp()函数用于对比字符串。
参数：（str1，str2);

如果str1 > str2,则返回正数，
如果str1 < str2,则返回负数，
如果str1 = str2,则返回0

以下为示例，输出为：3；

```c
#include <stdio.h>
#include <string.h>

int main(void){
	char str[80],str1[30];
	strcpy(str,  "woshidefg");
	strcpy(str1,  "woshiaaaa");
	int a = strcmp(str, str1);
	printf("%d\n",a);
	return 0;
}
```

# strcpy()
strcmp()函数用于字符串拷贝。
参数：（str1，str2);
以下为示例，输出为：woshiyigehaoren；

```c
#include <stdio.h>
#include <string.h>

int main(void){
	char str[80],str1[30];
	strcpy(str,  "woshiyigehaoren");
	printf("%s\n",str);
	return 0;
}
```

# strcpy()
strcmp()函数用于字符串拷贝。
参数：（str1，str2);
以下为示例，输出为：woshiyigehaoren；

```c
#include <stdio.h>
#include <string.h>

int main(void){
	char str[80];
	char str1[] = "woshiyigehaoren";
	strncpy(str,str1,30);
	printf("%s\n",str);
	return 0;
}
```
# sprintf()
sprintf()函数用于字符串拷贝。
参数（ s, str1, str2);
以下为示例，输出为：woshiyigehaoren；

```c
#include <stdio.h>
#include <string.h>

int main(void){
	char str[80];
	char str1[] = "woshiyigehaoren";
	strncpy(str,str1,30);
	char s[30];
	sprintf(s,"%s\n",str);
	printf("%s\n",s);
	return 0;
}
```

