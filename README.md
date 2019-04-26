# Amazon-Aws-EMR-Xgboost-Build-Recipe

Works on EMR version `5.18.0`

On EMR version 5.18.0

1. Install `libcurl`
```
    sudo yum install -y libcurl-devel.x86_64
```

2. Install cmake
```
    cd
    wget https://cmake.org/files/v3.14/cmake-3.14.3.tar.gz
    tar xzf cmake-3.14.3.tar.gz
    cd cmake-3.14.3
    ./bootstrap --prefix=/usr
    make -j4
    sudo make install
```
Run `cmake --version` should show `cmake version 3.14.3`

3. Install maven
```
    cd
    wget https://www-us.apache.org/dist/maven/maven-3/3.6.0/binaries/apache-maven-3.6.0-bin.tar.gz
    sudo tar xf apache-maven-3.6.0.tar.gz -C /opt
    sudo ln -s /opt/apache-maven-3.6.0 /opt/maven
    export M2_HOME=/opt/maven
    export MAVEN_HOME=/opt/maven
    export PATH=${M2_HOME}/bin:${PATH}
```

4. Clone XGBoost
```
    cd
    git clone --recursive https://github.com/dmlc/xgboost
```

5. Set `JAVA_HOME`
```
    export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk.x86_64
    export JAVA_JVM_LIBRARY=$JAVA_HOME/jre/lib/amd64/server/libjvm.so
    export JAVA_INCLUDE_PATH=$JAVA_HOME/include
    export JAVA_INCLUDE_PATH2=$JAVA_HOME/include/linux
```

6. Edit `make/config.mk` to support s3
```
    vim xgboost/make/config.mk
    
    # whether use AWS S3 support during compile
    USE_S3 = 1
```

7. Build xgboost
```
    cd xgboost
    make -j4
```

8. Edit `jvm-packages/create_jni.py` to support s3
```
    vim jvm-packages/create_jni.py
    
    CONFIG = {
        "USE_OPENMP": "ON",
        "USE_HDFS": "OFF",
        "USE_AZURE": "OFF",
        "USE_S3": "ON",
    
        "USE_CUDA": "OFF",
        "JVM_BINDINGS": "ON"
    }
```

9. Build xgboost for Java
```
    cd jvm-packages
    mvn package
    # use mvn package -DskipTests to skip any test
```

10. Locate jar
```
    find . -name "*.jar"
```

11. Zip and upload them to s3
```
    cd ../../
    zip -r xgboost.zip xgboost
    
    # upload to s3
```
