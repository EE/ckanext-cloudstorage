# ckanext-cloudstorage

Implements support for using S3, Azure, or any of 30+ providers supported by
[libcloud][] to [CKAN][].

# Setup

After installing `ckanext-cloudstorage`, add it to your list of plugins in
your `.ini`:

    ckan.plugins = stats cloudstorage

If you haven't already, setup [CKAN file storage][ckanstorage] or the file
upload button will not appear.

Every driver takes two options, regardless of which one you use. Both
the name of the driver and the name of the container/bucket are
case-sensitive:

    ckanext.cloudstorage.driver = AZURE_BLOBS
    ckanext.cloudstorage.container_name = demo

You can find a list of driver names [here][storage] (see the `Provider
Constant` column.)

Each driver takes its own setup options. See the [libcloud][] documentation.
These options are passed in using `driver_options`, which is a Python dict.
For most drivers, this is all you need:

    ckanext.cloudstorage.driver_options = {"key": "<your public key>", "secret": "<your secret key>"}

If you're using Azure, you can install the `azure-storage` module to get
improved behaviour. If the azure-storage module is found, then shared access
urls can be enabled, allowing the container to disable public access and
instead generating one-use URLs that expire after an hour. This prevents users
that do not have access to a resource from viewing them.

    ckanext.cloudstorage.use_secure_urls = 1


# Support

Most libcloud-based providers should work out of the box, but only those listed
below have been tested:

| Provider | Uploads | Downloads | Secure URLs (private resources) |
| --- | --- | --- | --- |
| Azure    | YES | YES | YES (if azure-storage is installed) |
| AWS S3   | YES | YES | No |
| Rackspace | YES | YES | No |

# Notes

1. You should disable public listing on the cloud service provider you're
   using, if supported.
2. Currently, only resources are supported. This means that things like group
   and organization images still use CKAN's local file storage.

# FAQ

- *DataViews aren't showing my data!* - did you setup CORS rules properly on
  your hosting service? ckanext-cloudstorage can try to fix them for you automatically,
  run:

        paster --plugin=ckanext-cloudstorage cloudstorage fix-cors <list of your domains> -c=<CKAN config>

[libcloud]: https://libcloud.apache.org/
[ckan]: http://ckan.org/
[storage]: https://libcloud.readthedocs.io/en/latest/storage/supported_providers.html
[ckanstorage]: http://docs.ckan.org/en/latest/maintaining/filestore.html#setup-file-uploads