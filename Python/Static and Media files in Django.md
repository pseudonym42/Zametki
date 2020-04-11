## Static files:

There are a couple main variables you need to be aware of when setting up static files management in Django. The way files being stored and delivered depends on DEBUG value i.e. during development the static files can be delivered differently compared to production environment.
STATIC_ROOT is used to indicate where static files should be collected. It is required only on production and only if files being delivered from the local file system by say your web server (e.g. Apache or nginx). For example you can setup Apache to deliver static files from /var/www/html. As the static files might live in different locations people normally use `manage.py collectstatic` to collect all the static files from the places listed in STATICFILES_DIRS into STATIC_ROOT (e.g. /var/www/html).

During development though - it is not being used if you have “django.contrib.staticfiles” (and you always have it unless specifically removed) in your installed apps, but if you specifically removed it from installed apps you still can deliver locally but in this case STATIC_ROOT needs to be setup, once it is setup in settings you need to add the following to the project `urls` file:

    static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)

The above will be enough for you to deliver the static files during development, because `runserver` will magically deliver them, but note that without “django.contrib.staticfiles” in your installed apps autodiscover does not work so you need to place all the static files in STATIC_ROOT yourself. On production `runserver` is obviously not being used.

STATICFILES_STORAGE is a storage ‘engine’, it indicates where static files to be stored. Default value (i.e. you do not even need to assign any value in settings) is django.contrib.staticfiles.storage.StaticFilesStorage, which is designed to store the files locally and it uses STATIC_ROOT and STATIC_URL (see its meaning below). But you can override it with some popular storages, e.g. S3Boto3Storage. In this case you do not need STATIC_ROOT but still will use STATIC_URL.

STATICFILES_DIRS is just a list of directories from which `manage.py collectstatic` can collect all the static files

STATIC_URL is a prefix attached to the path indicated in the template (it is assumed that you use ‘static’ template tag). For example it can be “/static/” and on production if your files collected and stored locally and being delivered by Apache then you will be delivering them from /var/www/html/static. The value of STATIC_URL can be a full link though e.g.

    https://<your_bucket_name>.s3.amazonaws.com/

if you use S3Boto3Storage as your STATICFILES_STORAGE. With this setup file paths shown in templates will be appended to the above url and you will be able to deliver files directly from S3.

## Media files:
Media files are normally the files that get uploaded by users via FileField and ImageField fields on models. As far as your database is concerned, these are just char columns storing relative paths, but the fields wrap that with code to use the media file storage class. Unlike static template tag for static files, the media files normally use `url` attribute to display the link. E.g. your ImageField name is avatar so in template you can use {{ object.avatar.url }}.

MEDIA_ROOT is required for both development and production. Indicates location where the media files to be stored once uploaded by the user. It can be just a path if default DEFAULT_FILE_STORAGE is used (django.core.files.storage.FileSystemStorage) e.g. /media/ or a url if for example S3Boto3Storage is used, e.g.

    https://<your_bucket_name>.s3.amazonaws.com/media

DEFAULT_FILE_STORAGE is the storage ‘engine’, which stores files in MEDIA_ROOT, under a subdirectory named by the field's upload_to value. Default value is django.core.files.storage.FileSystemStorage for storing files locally, for remote storage S3Boto3Storage can be used. When the file's url attribute is accessed, it returns the value of MEDIA_URL, prepended to the file's path inside MEDIA_ROOT.
So for example for S3Boto3Storage:

    MEDIA_ROOT = ‘https://<your_bucket_name>.s3.amazonaws.com/media/’
    MEDIA_URL = https://<your_bucket_name>.s3.amazonaws.com/media/
    uploaded_to = ‘abc’

So the link/path would be:

    https://<your_bucket_name>.s3.amazonaws.com/media/abc

And it will be stored inside

    https://<your_bucket_name>.s3.amazonaws.com/media/abc

Or for example for local storage (default):

	MEDIA_ROOT = ‘/var/some_folder/media/’
    MEDIA_URL = ‘/media/’
    uploaded_to = ‘abc’

The link/path would be:

    /media/abc

And it will be stored inside

	/var/some_folder/media/abc

Also note that to serve files locally you might need to add the following to the project urls file:

    static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)

For more info: https://www.caktusgroup.com/blog/2017/08/28/advanced-django-file-handling/
