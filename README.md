# Symfony2/Symfony3 LiipImagineSerialization Bundle

[![Build Status](https://img.shields.io/scrutinizer/build/g/Bukashk0zzz/LiipImagineSerializationBundle.svg?style=flat-square)](https://travis-ci.org/Bukashk0zzz/LiipImagineSerializationBundle)
[![Code Coverage](https://img.shields.io/codecov/c/github/Bukashk0zzz/LiipImagineSerializationBundle.svg?style=flat-square)](https://codecov.io/github/Bukashk0zzz/LiipImagineSerializationBundle)
[![Scrutinizer Code Quality](https://img.shields.io/scrutinizer/g/Bukashk0zzz/LiipImagineSerializationBundle.svg?style=flat-square)](https://scrutinizer-ci.com/g/Bukashk0zzz/LiipImagineSerializationBundle/?branch=master)
[![Dependency Status](https://www.versioneye.com/user/projects/56dfde26df573d00352c6813/badge.svg?style=flat)](https://www.versioneye.com/user/projects/56dfde26df573d00352c6813)
[![License](https://img.shields.io/packagist/l/Bukashk0zzz/liip-imagine-serialization-bundle.svg?style=flat-square)](https://packagist.org/packages/Bukashk0zzz/liip-imagine-serialization-bundle)
[![Latest Stable Version](https://img.shields.io/packagist/v/Bukashk0zzz/liip-imagine-serialization-bundle.svg?style=flat-square)](https://packagist.org/packages/Bukashk0zzz/liip-imagine-serialization-bundle)
[![Total Downloads](https://img.shields.io/packagist/dt/Bukashk0zzz/liip-imagine-serialization-bundle.svg?style=flat-square)](https://packagist.org/packages/Bukashk0zzz/liip-imagine-serialization-bundle)

[![SensioLabsInsight](https://insight.sensiolabs.com/projects/01074784-ea2d-4902-8a62-53a5f743bc6f/small.png)](https://insight.sensiolabs.com/projects/01074784-ea2d-4902-8a62-53a5f743bc6f)
[![knpbundles.com](http://knpbundles.com/Bukashk0zzz/LiipImagineSerializationBundle/badge-short)](http://knpbundles.com/Bukashk0zzz/LiipImagineSerializationBundle)

About
-----

Provides integration between [LiipImagineBundle](https://github.com/liip/LiipImagineBundle "LiipImagineBundle") and
[JMSSerializerBundle](https://github.com/schmittjoh/JMSSerializerBundle "JMSSerializerBundle").
Allows to generate full or relative URIs to entity fields mapped with `@Bukashk0zzz` and `@JMS` annotations during the serialization.
Also bundle supports [VichUploaderBundle](https://github.com/dustin10/VichUploaderBundle "VichUploaderBundle") field type.

Installation
------------

Add this to your `composer.json` file:

```json
"require": {
	"bukashk0zzz/liip-imagine-serialization-bundle": "dev-master",
}
```


Add the bundle to `app/AppKernel.php`

```php
$bundles = array(
	// ... other bundles
	new Bukashk0zzz\LiipImagineSerializationBundle\Bukashk0zzzLiipImagineSerializationBundle(),
);
```

Configuration
-------------

Add this to your `config.yml`:

```yaml
bukashk0zzz_liip_imagine_serialization:
    # Set true for generating url for vichUploader fields
    vichUploaderSerialize: false
    # Set true for generating url with host for vichUploader fields
    includeHost: false
    # Set true for adding original field value to object
    includeOriginal: false
    # Set true for adding host url to original value for vichUploader fields
    includeHostForOriginal: false
```


Usage
-----

Add the next class to the `use` section of your entity class.

```php
use Bukashk0zzz\LiipImagineSerializationBundle\Annotation as Bukashk0zzz;
```

Bundle provides two annotations which allow the serialization of url or `@Vich\UploadableField` fields in your entities.
At first you have to add `@Bukashk0zzz\LiipImagineSerializableClass` to the entity class which has image fields.
Then you have to add `@Bukashk0zzz\LiipImagineSerializableField` annotation to the field you want to serialize.

Annotation `@Bukashk0zzz\LiipImagineSerializableClass` does not have any option.  
Annotation `@Bukashk0zzz\LiipImagineSerializableField` has one required option *filter* which value should link to the LiipImagine filter.

It can be set like this `@Bukashk0zzz\LiipImagineSerializableField("photoFile")` or `@Bukashk0zzz\LiipImagineSerializableField(filter="photoFile")`. 
*filter* can be array of filters in this case serialized field will be also array.
For example if you add annotation `@Bukashk0zzz\LiipImagineSerializableField(filter={"big", "small"})` for field `image`  then you get:

```json
{
  "image": {
             "big": {}"/uploads/users/big/5659828fa80a7.jpg",
             "small": {}"/uploads/users/small/5659828fa80a7.jpg"
           }
}
```

Also there is another two options: 
- `vichUploaderField` - If you use VichUploaderBundle for your uploads you must specify link to the field with `@Vich\UploadableField` annotation 
- `virtualField` - By default serializer will override field value with link to filtered image. If you add `virtualField` option serializer will add to serialized object new field with name that you provided in this option and url to filtered image, original field in this case will be unattached. This option are required if you're using an array of filters.

And also don't forget that to serialize image fields they also should be marked with `@JMS` annotations to be serialized.

The generated URI by default:

```json
{
  "photo": "http://example.com/uploads/users/photos/5659828fa80a7.jpg",
  "cover": "http://example.com/uploads/users/covers/456428fa8g4a8.jpg"
}
```

The generated URI with `includeHost` set to `false`:

```json
{
  "photo": "/uploads/users/photos/5659828fa80a7.jpg",
  "cover": "/uploads/users/covers/456428fa8g4a8.jpg"
}
```

Example
-------

```php
<?php

namespace AppBundle\Entity;

use Doctrine\ORM\Mapping as ORM;
use JMS\Serializer\Annotation as JMS;
use Vich\UploaderBundle\Mapping\Annotation as Vich;
use Bukashk0zzz\LiipImagineSerializationBundle\Annotation as Bukashk0zzz;

/**
 * User Entity
 *
 * @ORM\Table(name="users")
 * @ORM\Entity()
 *
 * @Vich\Uploadable
 * @Bukashk0zzz\LiipImagineSerializableClass
 */
class User
{    
    /**
     * @var string $coverUrl Cover url
     *
     * @ORM\Column(type="string", length=255)
     *
     * @JMS\Expose
     * @JMS\SerializedName("cover")
     *
     * @Bukashk0zzz\LiipImagineSerializableField("thumb_filter")
     */
    public $coverUrl; 
    
    /**
     * @var string $photoName Photo name
     *
     * @ORM\Column(type="string", length=255)
     *
     * @JMS\Expose
     * @JMS\SerializedName("photo")
     *
     * @Bukashk0zzz\LiipImagineSerializableField("thumb_filter", vichUploaderField="photoFile")
     */
    public $photoName;

    /**
     * @var File $photoFile Photo file
     *
     * @JMS\Exclude
     *
     * @Vich\UploadableField(mapping="user_photo_mapping", fileNameProperty="photoName")
     */
    public $photoFile;
}
```

Copyright / License
-------------------

See [LICENSE](https://github.com/bukashk0zzz/LiipImagineSerializationBundle/blob/master/LICENSE)
