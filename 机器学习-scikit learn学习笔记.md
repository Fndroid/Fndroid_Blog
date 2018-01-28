# ����ѧϰ-scikit learnѧϰ�ʼ�

scikit-learn������http://scikit-learn.org/stable/

ͨ������£�һ��ѧϰ��������һ��ѧϰ�������ݣ������ͨ�����������ݵ�ѧϰ�����Զ�δ֪���ݽ���Ԥ�⡣

ѧϰ����һ����Է�Ϊ��

  * **�ලѧϰ��supervised learning��**
    * **���ࣨclassification��**
    * **�ع飨regression��**
  * **�Ǽලѧϰ��unsupervised learning��**
    * **���ࣨclustering��**

�ලѧϰ�ͷǼලѧϰ��������ǣ��ලѧϰ�У��������ݻ����ҪԤ��ı�ǩ��label�����������һ��è�͹���ͼƬ���Բ�ͬ����Ƭ������Ӧ�ı�ǩ��è�򹷣������Ǽලѧϰֻ�����һ��ͼƬ�������������ǩ��

����ͻع�������ǣ���������������еı�ǩ�д��ڵ���2�֣�����Ԥ������ֻ��Ҫ�ж����������ĸ��༴�ɣ����ع��������������һ�����������ı�����ɣ���������������������ƶ���ĳ��ȡ�

����һ����֪���⣬����ж���Ҫʹ�����ַ�����scikit-learn������һ��ͼ�����Ը������ͼ��ȷ�������ӣ�

http://scikit-learn.org/stable/tutorial/machine_learning_map/index.html

![](http://images2015.cnblogs.com/blog/852227/201704/852227-20170407102529128-818398503.png)

* * *



�����������ݰ�����

  1. **�������ݵĻ�ȡ������**
  2. **������ѵ����Ԥ��**
  3. **�־û�������**
  4. **�򵥽�����֤**
  5. **����**

**�� �������ݻ�ȡ������**

scikit-
learn�а����˺ܶ๩��ѧ��ѧϰ���������ݣ���Щ���ݰ�����sklearn.datasets���У��Ƚϵ��͵�������iris��������ݼ�������iris���Ļ������Ƭ�ĳ��ȺͿ�ȼ���Ӧ�������ࣺ

[![](http://images2015.cnblogs.com/blog/852227/201704/852227-20170407104004894-1161039153.png)](https://en.wikipedia.org/wiki/Iris_flower_data_set)

��ȡ������ݼ��ķ����ܼ򵥣�

    
    
     1 from sklearn import datasets
     2 
     3 # ��ȡiris���ݼ�
     4 iris = datasets.load_iris()
     5 # ��ȡ���ݼ��е�����ֵ���������Ƭ���ȿ�ȣ�
     6 iris_X = iris.data
     7 # ��ȡ���ݼ��еı�ǩ���ֱ������ֻ�
     8 iris_y = iris.target
     9 
    10 print(iris_X[::50])
    11 print(iris_y[::50])

 ��Ϊ���ݼ�������Ĳ��ҳ���Ϊ150��ÿ�ֻ�50���������Դ�ӡ��ʱ�򲽳�����Ϊ50�����ǿ��Կ������������ʾ��

![](http://images2015.cnblogs.com/blog/852227/201704/852227-20170407104525597-566612586.png)

��������������Ƕ�Ӧ�ķ��࣬�����Ϊ����123�ˡ�

��ѧϰ�У�Ҳ�����ֶ��Ĵ����Լ���Ҫ�����ݼ�����������һ��ع����ݣ�

    
    
    1 X, y = datasets.make_regression(n_samples=200, n_features=1, n_targets=1, noise=10)

�õ������ݼ���ͼ��ʾ��

![](http://images2015.cnblogs.com/blog/852227/201704/852227-20170407104858863-2135310558.png)

����ķ����ο�API�ĵ����ɣ�http://scikit-learn.org/stable/modules/classes.html#samples-
generator

**�� ѵ���������ͽ���Ԥ��  **

�������ǽ�iris���ݼ���Ϊ�����֣�һ��������ѵ������������һ���������������в��ԣ�����Ԥ�����ȷ����Ρ�

    
    
     1 from sklearn import datasets
     2 from sklearn.model_selection import train_test_split
     3 from sklearn.neighbors import KNeighborsClassifier
     4 
     5 # ��ȡiris���ݼ�
     6 iris = datasets.load_iris()
     7 # ��ȡ���ݼ��е�����ֵ���������Ƭ���ȿ�ȣ�
     8 iris_X = iris.data
     9 # ��ȡ���ݼ��еı�ǩ���ֱ������ֻ�
    10 iris_y = iris.target
    11 
    12 # �����ݷֱ�ѵ�����ݺͲ������ݣ���������Ϊ20%
    13 train_X, test_X, train_y, test_y = train_test_split(iris_X, iris_y, test_size=0.2)
    14 
    15 # ����K�ڽ�������
    16 knn = KNeighborsClassifier()
    17 # ����ѵ������
    18 knn.fit(train_X, train_y)
    19 # �õ�Ԥ���ǩ
    20 predicts = knn.predict(test_X)
    21 
    22 # �ԱȽ��
    23 print(predicts)
    24 print(test_y)
    25 
    26 # ����׼ȷ��
    27 print(knn.score(test_X, test_y))

�������Ƚ����ݷֳ�ѵ���Ͳ������ݣ����Ŵ�����һ��K�ٽ���������ͨ��fit��������ѵ�����ݣ�predict�����Բ������ݽ��в��ԡ�

�õ��Ľ����ͼ��

![](http://images2015.cnblogs.com/blog/852227/201704/852227-20170407110934894-1771202049.png)

�����׼ȷ�ʿ��ܻ᲻ͬ����Ϊʹ��train_test_split�����������ݻ�������ݼ�˳�������ѡ��

�����K�ٽ�����������һ���Ƚϼ򵥵ķ�����������ÿ��������������������ѡȡK����Ĭ����5���������ĵ���бȽϣ��ж��ĸ�����ĵ�࣬���ж�Ϊ��Ӧ���ࡣ��Ϊÿ���������ĸ����ԣ���������������Ҫһ����ά�ռ�����ϵ��ʾ�����Ǿ�����㹫ʽ�����ƶ�ά�ռ�ġ�

��Ȼ��scikit-learn���������ķ����������Բο�API�ĵ������ӣ�

http://scikit-learn.org/stable/supervised_learning.html#supervised-learning

**�� �־û�**

����һ��ѵ���õ�ģ�ͣ����Խ��г־û���Ҳ����ÿ����Ҫʹ��ģ�͵�ʱ�򣬲���Ҫ����ѵ����

�־û��ķ����ж��֣���������python�ṩ��pickle������scikit-learn�ṩ��Ч�ʸ��ߵ�joblib��

    
    
    1 from sklearn.externals import joblib
    2 # �־û�knn������
    3 joblib.dump(knn, 'save/knn.pk')
    4 # ��ȡknn������
    5 knn2 = joblib.load('save/knn.pk')
    6 print(knn2.score(test_X, test_y))

**�� ������֤**

������֤���Ƕ�ѵ�����ݺͲ������ݽ��ж�η������ģ�͵�׼ȷ�ʣ��ټ���ƽ��ֵ����ʾ��ǰģ�͵����ӡ�

һ��ģ���еĲ�ͬ�������᲻ͬ�̶ȵ�Ӱ��ģ�͵�׼ȷ�ʣ����ģ�ͼܹ����ò�ǡ����������ֹ�����ϣ�overfitting��������ϲ��㣨underfitting����

[![](http://images2015.cnblogs.com/blog/852227/201704/852227-20170407115138175-725106939.png)](https://zh.wikipedia.org/wiki/%E9%81%8E%E9%81%A9)

������֤������ͨ��cross_val_score������������֤��ÿ������ķ�����

    
    
     1 from sklearn import datasets
     2 from sklearn.model_selection import cross_val_score
     3 from sklearn.neighbors import KNeighborsClassifier
     4 
     5 iris = datasets.load_iris()
     6 X = iris.data
     7 y = iris.target
     8 
     9 knn = KNeighborsClassifier()
    10 
    11 # ������֤��������Ϊ5��
    12 scores = cross_val_score(knn, X, y, cv=5, scoring='accuracy')
    13 # �������ƽ��ֵ
    14 print(scores.mean())
    15 
    16 # ��K�ڽ���������k��1��30ȡֵ��������֤����
    17 k_range = range(1, 31)
    18 k_scores = []
    19 for r in k_range:
    20     # ����k��Ҳ����n_neighbors����
    21     kn = KNeighborsClassifier(n_neighbors=r)
    22     # ��ƽ��ֵ������k_scores��
    23     k_scores.append(cross_val_score(kn, X, y, cv=10, scoring='accuracy').mean())
    24 
    25 print(k_scores)

��ӡ�õ��Ľ����

![](http://images2015.cnblogs.com/blog/852227/201704/852227-20170407115804628-2142596024.png)

ʹ��print��ӡ�����Ȼ����ʾ�����ǲ�ֱ�ۣ��������ͨ��matplotlib�����������ͼ������ֱ�۵Ŀ���������ÿ��k���ĸ��ķ������ߣ�

    
    
    1 import matplotlib.pyplot as plt
    2 # ��k_rangeΪx��k_scoresΪy����㲢����
    3 plt.plot(k_range, k_scores)
    4 # ����x��y�ı�ǩ
    5 plt.xlabel('n_neighbors')
    6 plt.ylabel('score')
    7 # չʾͼ��
    8 plt.show()

![](http://images2015.cnblogs.com/blog/852227/201704/852227-20170407120221238-2105582155.png)

���Կ�����k����Խ��Խ�ã����ǵõ����ͼ���Ϳ���ѡ��һ���÷ָ߶�Ӧ��k��������ͼ��13��Ϊģ�͵�n_neighbors������

**�� ����**

�������Ҳ��datasets�а����ģ���д�����ַ��ࡣ����ʹ�ùٷ���һ�����ӣ�

http://scikit-
learn.org/stable/auto_examples/classification/plot_digits_classification.html#sphx-
glr-auto-examples-classification-plot-digits-classification-py

ѵ������֮�󣬳־û�ģ�ͣ����ſ��Գ���Ԥ��һ�������Լ���Ƶ����֣�8*8���ػҶ�ͼƬ���Ŵ�ģ���ˣ���

![](http://images2015.cnblogs.com/blog/852227/201704/852227-20170407121233050-1861823033.jpg)

���Դ��룺

    
    
     1 from PIL import Image
     2 import numpy as np
     3 from sklearn.externals import joblib
     4 
     5 # ʹ��pil��ȡÿ�����ص����ɫ��Ϣ���Ҷ�ͼΪ��ά���飩
     6 img = Image.open('four.jpg')
     7 img = np.array(img)
     8 for i in range(8):
     9     for j in range(8):
    10         # ����datasets�е������޸�ͼƬ������Ϣ
    11         img[i][j] //= 16
    12         img[i][j] = 15 - img[i][j]
    13 
    14 clf = joblib.load('save/digit')
    15 print('�ƶ�ͼƬ�е�����Ϊ��', clf.predict(img.reshape((1, -1))))

�����

![](http://images2015.cnblogs.com/blog/852227/201704/852227-20170407121548519-1935582085.png)



**��󣬹��ڻع�;����demo�����Բο��ٷ���Examples��**

**http://scikit-learn.org/stable/auto_examples/index.html**



**������ָ������ָ�л**

