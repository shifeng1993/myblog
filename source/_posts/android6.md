---
title: 【android学习随笔】android列表组件RecyclerView
categories: android
date: 2018-05-07
tags:
  - android
---
# 基本用法
## 添加依赖
`app/build.gradle`
```java
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile 'com.android.support:appcompat-v7:24.2.1'
    compile 'com.android.support:recyclerview-v7:24.2.1'
    testCompile 'junit:junit:4.12'
}
```
关于 `compile` android在8.0改成了 `implementation` 所以8.0+可以写成以下代码
```java
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation 'com.android.support:appcompat-v7:26.1.0'
    implementation 'com.android.support:recyclerview-v7:26.1.0'
    testImplementation 'junit:junit:4.12'
}
```

然后点击sync now，同步编译配置

## 添加xml布局
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android" android:layout_width="match_parent" android:layout_height="match_parent">
    <android.support.v7.widget.RecyclerView
        android:id="@+id/recycler_view"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
</LinearLayout>
``` 

## 添加item类
```java
public class Fruit {

    private String name;
    private int imageId;

    public Fruit(String name, int imageId) {
        this.name = name;
        this.imageId = imageId;
    }

    public String getName() {
        return name;
    }

    public int getImageId() {
        return imageId;
    }

}
```

## 定义item布局
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android" android:layout_width="match_parent" android:layout_height="wrap_content">
    <ImageView
        android:id="@+id/fruit_image"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
    <TextView
        android:id="@+id/fruit_name"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center_vertical"
        android:layout_marginLeft="10dp" />
</LinearLayout>
```

## 定义适配器类FruitAdapter 继承自 RecyclerView.Adapter
```java
public class FruitAdapter extends RecyclerView.Adapter<FruitAdapter.ViewHolder> {
    private List<Fruit> mFruitList;

    // 定义了一个内部类 ViewHolder 继承自 RecyclerView.ViewHolder
    static class ViewHolder extends RecyclerView.ViewHolder {
        ImageView fruitImage;
        TextView fruitName;

        public ViewHolder(View view) {
            super(view);
            fruitImage = (ImageView) view.findViewById(R.id.fruit_image);
            fruitName = (TextView) view.findViewById(R.id.fruit_name);
        }
    }

    // 通过 FruitAdapter 构造函数把需要展示的源数据传递进来，并赋值给当前类的私有变量 mFruitList
    public FruitAdapter(List<Fruit> fruitList) {
        mFruitList = fruitList;
    }

    // 先加载item布局，然后创建ViewHolder实例，
    @Override
    public ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(parent.getContext()).inflate(R.layout.fruit_item, parent, false);
        ViewHolder holder = new ViewHolder(view);
        return holder;
    }

    // 对item进行赋值，会在每个子项被滚动到屏幕内的时候执行，使用position得到当前item实例，将数据灌入ViewHolder的布局中
    @Override
    public void onBindViewHolder(ViewHolder holder, int position) {
        Fruit fruit = mFruitList.get(position);
        holder.fruitImage.setImageResource(fruit.getImageId());
        holder.fruitName.setText(fruit.getName());
    }

    // 返回数据源的数组长度
    @Override
    public int getItemCount() {
        return mFruitList.size();
    }
}
```

## 修改mainActivity引入
```java
public class MainActivity extends AppCompatActivity {
    private List<Fruit> fruitList = new ArrayList<>();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        initFruits();                                                                // 初始化水果数据
        RecyclerView recyclerView = (RecyclerView) findViewById(R.id.recycler_view); // 获取RecyclerView实例
        LinearLayoutManager layoutManager = new LinearLayoutManager(this);           // 创建layoutManager对象，
        recyclerView.setLayoutManager(layoutManager);                                // 指定布局方式
        FruitAdapter adapter = new FruitAdapter(fruitList);                          // list数据传到适配器中
        recyclerView.setAdapter(adapter);                                            // 完成适配器设置
    }
    // mock一个列表
    private void initFruits() {
        for (int i = 0; i < 100; i++) {
            Fruit apple = new Fruit("Apple", R.drawable.ic_accessibility_black_24dp);
            fruitList.add(apple);
            Fruit banana = new Fruit("Banana", R.drawable.ic_accessibility_black_24dp);
            fruitList.add(banana);
            Fruit orange = new Fruit("Orange", R.drawable.ic_accessibility_black_24dp);
            fruitList.add(orange);
            Fruit watermelon = new Fruit("Watermelon", R.drawable.ic_accessibility_black_24dp);
            fruitList.add(watermelon);
            Fruit pear = new Fruit("Pear", R.drawable.ic_accessibility_black_24dp);
            fruitList.add(pear);
            Fruit grape = new Fruit("Grape", R.drawable.ic_accessibility_black_24dp);
            fruitList.add(grape);
            Fruit pineapple = new Fruit("Pineapple", R.drawable.ic_accessibility_black_24dp);
            fruitList.add(pineapple);
            Fruit strawberry = new Fruit("Strawberry", R.drawable.ic_accessibility_black_24dp);
            fruitList.add(strawberry);
            Fruit cherry = new Fruit("Cherry", R.drawable.ic_accessibility_black_24dp);
            fruitList.add(cherry);
            Fruit mango = new Fruit("Mango", R.drawable.ic_accessibility_black_24dp);
            fruitList.add(mango);
        }
    }
}
```
# 实现横向滚动和瀑布流
## 横向滚动
### fruit_item.xml改成垂直布局
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="100dp"
    android:layout_height="wrap_content"
    android:orientation="vertical">

    <ImageView
        android:id="@+id/fruit_image"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center_horizontal"
        android:layout_marginTop="10dp" />

    <TextView
        android:id="@+id/fruit_name"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center_horizontal"
        android:layout_marginTop="10dp" />
</LinearLayout>
```
### MainActivity 设置布局
```java
public class MainActivity extends AppCompatActivity {
    private List<Fruit> fruitList = new ArrayList<>();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        initFruits();                                                                // 初始化水果数据
        RecyclerView recyclerView = (RecyclerView) findViewById(R.id.recycler_view); // 获取RecyclerView实例
        LinearLayoutManager layoutManager = new LinearLayoutManager(this);           // 创建layoutManager对象，
        layoutManager.setOrientation(LinearLayoutManager.HORIZONTAL);                // 修改方向
        recyclerView.setLayoutManager(layoutManager);                                // 指定布局方式
        FruitAdapter adapter = new FruitAdapter(fruitList);                          // list数据传到适配器中
        recyclerView.setAdapter(adapter);                                            // 完成适配器设置
    }
    ...
}
```
## 瀑布流
### 修改fruit_item.xml
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    android:layout_margin="5dp">

    <ImageView
        android:id="@+id/fruit_image"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center_horizontal"/>

    <TextView
        android:id="@+id/fruit_name"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="left"
        android:layout_marginTop="10dp" />
</LinearLayout>
```
### MainActivity 设置布局
```java
public class MainActivity extends AppCompatActivity {
    private List<Fruit> fruitList = new ArrayList<>();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        initFruits();                                                                // 初始化水果数据
        RecyclerView recyclerView = (RecyclerView) findViewById(R.id.recycler_view); // 获取RecyclerView实例
        StaggeredGridLayoutManager layoutManager = new StaggeredGridLayoutManager(3, StaggeredGridLayoutManager.VERTICAL); //三列垂直布局的瀑布流
        recyclerView.setLayoutManager(layoutManager);                                // 指定布局方式
        FruitAdapter adapter = new FruitAdapter(fruitList);                          // list数据传到适配器中
        recyclerView.setAdapter(adapter);                                            // 完成适配器设置
    }
    // mock一个列表
    private void initFruits() {
        for (int i = 0; i < 100; i++) {
            Fruit apple = new Fruit(getRandomLengthName("Apple"), R.drawable.ic_accessibility_black_24dp);
            fruitList.add(apple);
            Fruit banana = new Fruit(getRandomLengthName("Banana"), R.drawable.ic_accessibility_black_24dp);
            fruitList.add(banana);
            Fruit orange = new Fruit(getRandomLengthName("Orange"), R.drawable.ic_accessibility_black_24dp);
            fruitList.add(orange);
            Fruit watermelon = new Fruit(getRandomLengthName("Watermelon"), R.drawable.ic_accessibility_black_24dp);
            fruitList.add(watermelon);
            Fruit pear = new Fruit(getRandomLengthName("Pear"), R.drawable.ic_accessibility_black_24dp);
            fruitList.add(pear);
            Fruit grape = new Fruit(getRandomLengthName("Grape"), R.drawable.ic_accessibility_black_24dp);
            fruitList.add(grape);
            Fruit pineapple = new Fruit(getRandomLengthName("Pineapple"), R.drawable.ic_accessibility_black_24dp);
            fruitList.add(pineapple);
            Fruit strawberry = new Fruit(getRandomLengthName("Strawberry"), R.drawable.ic_accessibility_black_24dp);
            fruitList.add(strawberry);
            Fruit cherry = new Fruit(getRandomLengthName("Cherry"), R.drawable.ic_accessibility_black_24dp);
            fruitList.add(cherry);
            Fruit mango = new Fruit(getRandomLengthName("Mango"), R.drawable.ic_accessibility_black_24dp);
            fruitList.add(mango);
        }
    }

    // 返回随机长度的字符串
    private String getRandomLengthName(String name) {
        Random random = new Random();
        int length = random.nextInt(20) + 1;
        StringBuilder builder = new StringBuilder();
        for (int i = 0; i < length; i++) {
            builder.append(name);
        }
        return builder.toString();
    }

}
```

# 实现子view的点击事件
## FruitAdapter
```java
public class FruitAdapter extends RecyclerView.Adapter<FruitAdapter.ViewHolder> {
    private List<Fruit> mFruitList;

    // 定义了一个内部类 ViewHolder 继承自 RecyclerView.ViewHolder
    static class ViewHolder extends RecyclerView.ViewHolder {
        View fruitView;
        ImageView fruitImage;
        TextView fruitName;

        public ViewHolder(View view) {
            super(view);
            fruitView = view;                                  // 新建一个view容器
            fruitImage = (ImageView) view.findViewById(R.id.fruit_image);
            fruitName = (TextView) view.findViewById(R.id.fruit_name);
        }
    }

    // 通过 FruitAdapter 构造函数把需要展示的源数据传递进来，并赋值给当前类的私有变量 mFruitList
    public FruitAdapter(List<Fruit> fruitList) {
        mFruitList = fruitList;
    }

    // 先加载item布局，然后创建ViewHolder实例，
    @Override
    public ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(parent.getContext()).inflate(R.layout.fruit_item, parent, false);
        final ViewHolder holder = new ViewHolder(view);         // 给容器一个只读的实例
        // 给容器添加点击事件
        holder.fruitView.setOnClickListener(new View.OnClickListener() {
            public void onClick(View v) {
                int position = holder.getAdapterPosition();
                Fruit fruit = mFruitList.get(position);
                Toast.makeText(v.getContext(), "you clicked view " + fruit.getName(), Toast.LENGTH_SHORT).show();
            }
        });
        // 给容器内图片添加点击事件
        holder.fruitImage.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                int position = holder.getAdapterPosition();
                Fruit fruit = mFruitList.get(position);
                Toast.makeText(v.getContext(), "you clicked image " + fruit.getName(), Toast.LENGTH_SHORT).show();
            }
        });
        return holder;
    }
    ...
}
```



