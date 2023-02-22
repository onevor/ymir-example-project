# This is an example implementation of Ymir

# Replicate this in your own project

## Setup
Install the Ymir CLI
```
npm i -g @onevor/ymir-core-cli
```

You can set it up in an existing project or create a new npm project:
```
npm init
```
Ymir does not require an npm project to work, you can install all packages globally

If you install the plugins before `ymir init` Ymir will automaticity register them.
```
npm i -D @onevor/ymir-plugin-ssm @onevor/ymir-plugin-azure-key-vault
```

Set up Ymir in your project
```
ymir init
```

## The plugins needs some config
SSM needs the region you are hosting SSM in, you can add this to Ymir like so:
```
ymir config resolver ssm -c region=eu-north-1
```
Azure key vault needs the name of the keyvault you want to connect to:
```
ymir config resolver keyvault -c vault_name=ymir-test-vault
```

You should also set one of the plugins as the default resolver you can do that by adding the `--default|-d` flag to one of the above commands or do it like:
```
ymir config resolver ssm -d
```

## Create an .env file with some test data in it;
Create an env file if you do not already have one in your project:
```
TOP_SECRET_VALUE=this-is-a-secret-value-for-dev
PORT=3000
DB_CONNECTION_STRING=mongodb://localhost:27017/dev
```

Now you can import your file to Ymir:
```
ymir import --path .env
```

By default you are checked out to dev when you init a new Ymir project.
You can checkout a different stack before importing or you can add `--stack|-s` to the import command to add your .env file to a different stack.

In my project i imported three .env files. 
One for dev, one for stage and one for prod.

You can also add one at a time to your stack(s)
I added DEBUG and AZURE_KEY to the default stack like so:
```
ymir add DEBUG -v 0 -s default
ymir add AZURE_KEY -v this-is-a-value-from-azure -r keyvault -s default
```

If you now checkout a stack like:
```
ymir checkout stage
```

You should have a file `.env.ymir` unless you imported any .env file to `stage` you should now only have the props from the default stack.

You can checkout `dev` again, and you should have the content of `.env` plus the props from the default stack in your `.env.ymir`.

You can also update your `.env.ymir` file with out checking out stacks by running:
```
ymir export
```

If you want Ymir to write to `.env` like I do, you can change the config by:
```
ymir config file .env
```

Now if you `export` or `checkout` a stack; your `.env` file will be overwritten. Ymir assumes you never directly edit your `.env` file, if you do ymir will overwrite it.
