- [Назад](/./android.md)

## Build types, flavours, flavour dimension, build variant

**Build type** используется, чтобы задать настройки сборки (использование proguard, сертификат подписи apk и т.д.). Часто используют debug и release. Задается параметром **buildTypes**

**Flavour** используются, для определения сборок с разной функциональностью (платные фичи, разные ресурсы). Задается параметром **productFlavour**

```java
buildTypes {
	debug {
		applicationIdSuffix ".debug"
		debuggable true
		minifyEnabled false
	}
	release {
		minifyEnabled false
		proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
	}
}

productFlavors {
	free {
		applicationId "ru.kazinaki.free"
		resValue "string", "flavored_app_name", "Free App"
	}

	paid {
		applicationId "ru.kazinaki.paid"
		resValue "string", "flavored_app_name", "Paid App"
	}
}
```

**BuildVariant** - это комбинация build type и flavour. Для данного примера получаем четыре **BuildVariant**: freeDebug, freeRelease, paidDebug, paidRelease.

**Flavour dimension** - это дополнительное разделение на основе задаваемых dimension (измерений).

```java
android {
	buildTypes {
		debug {
		}

		release {
		}
	}
	   
	flavourDimensions "api", "version"
	
	productFlavors {
		free {
			dimension "version"
		}

		paid {
			dimension "version"
		}
	   
		minApi24 {
			dimension "api"
		}

		minApi21 {
			dimension "api"
		}
	}
}
```
	
Заданы два flavorDimensions: api и version. Порядок flavorDimensions определяет приоритет. В каждом dimension заданы по два flavor. Dimensions комбинируются между собой, поэтому в результате такой конфигурации создаются следующие build-варианты (dimensions идут в порядке убывания приоритета): minApi21/minApi24 Free/Paid Debug/Release, например minApi21FreeRelease.

**Фильтрация вариантов**

В данном примере создается 8 вариантов билда, если не все они нужны, то можно отфильтровать некоторые, используя блок **varinatFilter**. В нем можно пройти по всем вариантам билдов, проверить имена build и flavour, и отфильтровать ненужные, с помощью метода **setIgnored(true)**

```java
varinatFilter { variant ->
	def flavourNames = variant.flavour.name
	def buildTypeNames = variant.buildType.name
	if (flavourNames.contains("minApi21") && buildTypeNames.contains("debug)) {
		setIgnore("true")
	}
```

в данном случает отфильтруются сборки с именами minApi21FreeDebug и minApi21PaidDebug.
