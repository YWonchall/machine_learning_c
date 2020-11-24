> 本篇介绍目前为止的最后一个算法，逻辑回归。后边如果有机会可能会继续更新。
>
> 同样使用随机梯度下降法。

## 算法介绍

模型可以如下表示：
$$
yhat = \frac{e^{b0+b1×x1}}{1+e^{b0+b1×x1}}
$$

可简化为(简单数学计算即可)
$$
yhat = \frac{1.0}{1.0+e^{-(b0+b1×x1)}}
$$

更新方程：
$$
b = b+learning\space rate × (y-yhat)×yhat×(1-yhat)×x
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



### 估计回归参数

更新方程
$$
b1(t+1)=b1(t)+learning\space rate ×(y(t)-yhat(t)×yhat(t)×(1-yhat(t))×x1(t)
$$

$$
b0(t+1)=b0(t)+learning\space rate ×(y(t)-yhat(t)×yhat(t)×(1-yhat(t))
$$



```c
// 输入参数：数据集、每个样本的属性个数（带label）、存放系数的数组、学习率、epoch、train_size
void coefficients_sgd(double ** dataset, int col, double *coef, double l_rate, int n_epoch, int train_size) {
	int i;
	for (i = 0; i < n_epoch; i++) {
		int j = 0;//遍历每一行
		for (j = 0; j < train_size; j++) {
			double yhat = predict(col,dataset[j], coef);
			double err = dataset[j][col - 1] - yhat;
			coef[0] += l_rate * err * yhat * (1 - yhat);
			int k;
			for (k = 0; k < col - 1; k++) {
				coef[k + 1] += l_rate * err * yhat * (1 - yhat) * dataset[j][k];
			}train_size
		}
	}
	for (i = 0; i < col; i++) {
		printf("coef[%d]=%f\n",i, coef[i]);
	}
}
```

### 预测函数

```c
// 输入参数：样本属性个数、样本、系数
double predict(int col, double array[], double coefficients[]) {//预测某一行的值
	double yhat = coefficients[0];
	int i;
	for (i = 0; i < col - 1; i++)
		yhat += coefficients[i + 1] * array[i];
    printf("%f",1 / (1 + exp(-yhat)));
	return 1 / (1 + exp(-yhat));
}
```

