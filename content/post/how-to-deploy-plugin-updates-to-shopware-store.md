+++
title = "How to deploy plugin updates automaticly to the Shopware Store"
date = "2019-09-18"
author = "Shyim"
description = "Automating deployment to Shopware Store"
+++

This blog post describes a small example how to automaticly deploy plugin updates to the Store. I will use the [FroshPerformance](https://github.com/FriendsOfShopware/FroshPerformance) plugin as example.

## Preparation

You will need

* a Plugin which based on the 5.2 Pluginsystem or Shopware 6 Plugin
* a Continuous Integration (i will use Travis as example)
* Credentials for the Shopware Account
* The **PLUGIN_ID** from the Account
* [FroshPluginUploader](https://github.com/FriendsOfShopware/FroshPluginUploader/)
    
### How to get the PLUGIN_ID?

* Visit the Account and Login
* Go to the Detail page of your Plugin
* Extract the id from the URL (e.g https://account.shopware.com/producer/plugins/ **8176**)

### Prepare the Plugin itself

You will need a `plugin.xml` with filled `title`, `version`, `description`, `compability` and `changelog` for the languages `en` and `de`. All these informations will be picked up automaticly and updated in the Account.

**Optional**

You can define also the plugin description, manual and images in `Resources/store`. Currently supported files are
* [shortLanguage (de, en)].html - for the Description
* [shortLanguage (de, en)]_manual.html - for the Install Manual
* images/*.(png|jpg|jpeg) will be used for Images. First image will be used as preview image.

### Setting up the ci

As first you should define following environment variables as secret in your favourite ci

* ACCOUNT_USER - Username of Shopware Account
* ACCOUNT_PASSWORD - Password of Shopware Account
* PLUGIN_ID - The obtained plugin id
    

As next we setup a job to build the store ready plugin zip. The Uploader has an command to generate an zip file for your
```
php frosh-plugin-uploader.phar plugin:zip:dir [Path to Git Folder]
```

After building an zip, we validate the zip file using the uploader with
```
php frosh-plugin-uploader.phar plugin:validate my-plugin.zip
```

This will check all requirements from the Uploader and also from the Plugin Guidelines. which are also describe in [Prepare the Plugin itself](#preparethepluginitself)

After the validating you can sync the **optional** folder `Resources/store` to the account with

```
php frosh-plugin-uploader.phar plugin:update FroshPerformance
```

As last step you can upload the zip with

```
php frosh-plugin-uploader.phar plugin:upload my-plugin.zip
```

The command will also wait for the automatic code-review and will fail, if its not green. Reuploading same version will replace existing binary.