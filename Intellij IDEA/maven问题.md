# IDEA Maven 问题

## Usage

### Generating the IntelliJ IDEA project files

To generate the files needed for an IntelliJ IDEA Project setup, you only need to execute the main plugin goal, which is `idea:idea` like so:

```
mvn idea:idea
```

The above command will execute the other three goals needed to setup your project for IntelliJ IDEA: `idea:project`, `idea:module`, and `idea:workspace`.

Please note that this plugin has no way to determine the name of the JDK you are using. By default, this uses the JDK name equivalent to `java -version` in your machine. If you know the name of the JDK to use, for example, 1.5, you can then execute:

```
mvn idea:idea -DjdkName=1.5
```

### Deleting IDEA project files

To delete the project files used by IntelliJ IDEA, you can execute the command below:

```
mvn idea:clean
```

### Creating the IDEA project files separately

If, for some reason, you want to create just one of the `ipr`, `iml`, or `iws` files, you can still do it.

To generate just the `ipr` file, use:

```
mvn idea:project
```

To generate just the `iml` files, use:

```
mvn idea:module
```

To generate just the `iws` file, use:

```
mvn idea:workspace
```