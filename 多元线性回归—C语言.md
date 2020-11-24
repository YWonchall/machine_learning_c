> 上篇已经介绍过简单线性回归了，这篇介绍第二个算法，多元线性回归，仅介绍两个主要函数：预测函数和训练函数。

## 算法介绍

模型可以如下表示：
$$
y = b_0+b_1×x_1+b_2×x_2+...
$$

## 随机梯度下降法

> 多元线性回归采用的是随机梯度下降方法，该方法具体介绍同样自行百度，在这里不做详细介绍。仅介绍C语言实现方法



这里给出更新方程：
$$
b = b-learning\space rate × error × x
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

	float mean = (float)(sum / length);
	return mean;
}
```

### 随机梯度下降估计回归系数

更新方程：
$$
error = prediction-expected\
$$

$$
b_1(t+1) = b_1(t) - learning\space rate × error(t) × x_1(t)
$$


$$
b_0(t+1) = b_0(t) - learning\space rate × error(t)
$$

```c
// 参数为：数据集、每个数据集属性个数(带label)、存放系数的数组、学习率、epoch、train_size
double* coefficients_sgd(double** dataset,int col,double coef[], double l_rate, int n_epoch, int train_size) {
	int i;
	for (i = 0; i < n_epoch; i++) {
		int j = 0;//遍历每一行
		for (j = 0; j < train_size; j++) {
			double yhat = predict(col,dataset[j], coef);
			double err = yhat - dataset[j][col - 1];
			coef[0] -= l_rate * err;
			int k;
			for (k = 0; k < col - 1; k++) {
				coef[k + 1] -= l_rate * err*dataset[j][k];
			}
		}
	}
	for (i = 0; i < col; i++) {
		printf("coef[i]=%f\n", coef[i]);
	}
	return coef;
}
```

### 预测函数

```c
// 参数：样本属性个数、样本、系数
double predict(int col,double array[], double coefficients[]) {//预测某一行的值
	double yhat = coefficients[0];
	int i;
	for (i = 0; i < col; i++)
		yhat += coefficients[i + 1] * array[i];
	return yhat;
}
```



