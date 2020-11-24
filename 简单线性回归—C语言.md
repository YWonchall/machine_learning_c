> 简单线性回归应该是最简单的机器学习算法了，在这里主要介绍一下算法主要函数的C语言实现，具体算法原理简单一提，如果要学习，可以自行百度。

## 算法介绍

模型可以如下表示：
$$
 y = b_0 + b_1 × x
$$
训练主要依据以下公式：
$$
B_1 = \frac{\sum_{i=1}^{n}{((x_i - mean(x))×(y_i - mean(y)))}}{\sum_{i=1}^{n}{(x_i - mean(x))^2}}
$$

$$
B_0 = mean(y) - B_1 × mean(x)
$$

## 函数

### 读取csv

- 以下三个函数分别为获取行数、获取列数、获取文本内容。

```C
double **dataset;
int row,col;

int get_row(char *filename)//获取行数
{
	char line[1024];
	int i = 0;
	FILE* stream = fopen(filename, "r");
	while(fgets(line, 1024, stream)){
		i++;
	}
	fclose(stream);
	return i;
}

int get_col(char *filename)//获取列数
{
	char line[1024];
	int i = 0;
	FILE* stream = fopen(filename, "r");
	fgets(line, 1024, stream);
	char* token = strtok(line, ",");
	while(token){
		token = strtok(NULL, ",");
		i++;
	}
	fclose(stream);
	return i;
}

void get_two_dimension(char* line, double** data, char *filename)
{
	FILE* stream = fopen(filename, "r");
	int i = 0;
	while (fgets(line, 1024, stream))//逐行读取
    {
    	int j = 0;
    	char *tok;
        char* tmp = strdup(line);
        for (tok = strtok(line, ","); tok && *tok; j++, tok = strtok(NULL, ",\n")){
        	data[i][j] = atof(tok);//转换成浮点数
		}//字符串拆分操作
        i++;
        free(tmp);
    }
    fclose(stream);//文件打开后要进行关闭操作
}

```

EXAMPLE

```C
int main()
{
	char filename[] = "data.csv";
    char line[1024];
    double **data;
    int row, col;
    row = get_row(filename);
    col = get_col(filename);
    data = (double **)malloc(row * sizeof(int *));
	for (int i = 0; i < row; ++i){
		data[i] = (double *)malloc(col * sizeof(double));
	}//动态申请二维数组
	get_two_dimension(line, data, filename);
	printf("row = %d\n", row);
	printf("col = %d\n", col);

	int i, j;
	for(i=0; i<row; i++){
		for(j=0; j<col; j++){
			printf("%f\t", data[i][j]);
		}
		printf("\n");
    }
}
```

### 计算均值

$$
mean(x) = \frac{\sum_{i=1}{x_i}}{count(x)}
$$

```c
float mean(float* values, int length) {//对一维数组求均值
	int i;
	float sum = 0.0;
	for (i = 0; i < length; i++) {
		sum += values[i];
	}
	float mean = (float)(sum / length);
	return mean;
}
```

### 计算方差

$$
variance = \sum_{i=1}^{n}{(x_i - mean(x))^2}

$$

```C
float variance(float* values, float mean, int length) {//这里求的是平方和，没有除以n
	float sum = 0.0;
	int i;
	for (i = 0; i < length; i++) {
		sum += (values[i] - mean)*(values[i] - mean);
	}
	return sum;
}
```



EXAMPLE

```C
float x[5]={1,2,4,3,5};
printf("%f\n",mean(x, 5));
printf("%f",variance(x,mean(x,5),5));
```

### 计算协方差

$$
covariance = {\sum_{i=1}^{n}{((x_i - mean(x))}}×(y_i - mean(y)))
$$

```c
float covariance(float* x, float mean_x, float* y, float mean_y, int length) {
	float cov = 0.0;
	int i = 0;
	for (i = 0; i < length; i++) {
		cov += (x[i] - mean_x)*(y[i] - mean_y);
	}
	return cov;
} 
```

EXAMPLE

```c
float x[5]={1,2,4,3,5};
float y[5]={1,3,3,2,5};
printf("%f",covariance(x,mean(x,5),y,mean(y,5),5));
```

### 估计回归参数

$$
B_1 = \frac{covariance(x,y)}{variance(x)}
$$

```c
//由均值方差估计回归系数
// 输入参数：数据、存放系数的数组、数据个数
void coefficients(float** data, float* coef, int length) {
	float* x = (float*)malloc(length * sizeof(float));
	float* y = (float*)malloc(length * sizeof(float));
	int i;
	for (i = 0; i < length; i++) {
		x[i] = data[i][0];
		y[i] = data[i][1];
		//printf("x[%d]=%f,y[%d]=%f\n",i, x[i],i,y[i]);
	}
	float x_mean = mean(x, length);
	float y_mean = mean(y, length);
	//printf("x_mean=%f,y_mean=%f\n", x_mean, y_mean);
	coef[1] = covariance(x, x_mean, y, y_mean, length) / variance(x, x_mean, length);
	coef[0] = y_mean - coef[1] * x_mean;
	for (i = 0; i < 2; i++) {
		printf("coef[%d]=%f\n", i, coef[i]);
	}
}
```

