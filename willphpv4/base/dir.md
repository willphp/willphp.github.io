### Directory Structure
```
www  WEB deployment directory (or subdirectory)
├─app                   Application directory
│  ├─index              Default application
│  │  ├─config          Configuration
│  │  ├─controller      Controllers
│  │  ├─model           Models
│  │  ├─view            Views
│  │  └─widget          Widgets
│  └─common.php         Common functions
│
├─config                Configuration directory
│  ├─app.php            Application
│  ├─cache.php          Cache
│  ├─cookie.php         Cookie
│  ├─database.php       Database
│  ├─debugbar.php       Debug bar
│  ├─filter.php         Automatic filtering
│  ├─middleware.php     Middleware
│  ├─page.php           Pagination
│  ├─response.php       Response
│  ├─route.php          Routing
│  ├─session.php        Session
│  ├─site.php           Website  
│  ├─template.php       Template engine
│  ├─upload.php         Upload settings
│  ├─validate.php       Validation rules
│  └─view.php           View
│
├─public                WEB directory (public access directory)
│  ├─static             Website static resource directory (css, js, img)
│  ├─uploads            Image upload directory (writable)
│  ├─index.php          Entry file
│  ├─.htaccess          Apache rewrite
│  └─nginx.htaccess     Nginx rewrite
|
├─extend                Extended library        
├─middleware            Middleware
├─route                 Routing configuration
├─runtime               Application runtime directory (writable)
├─template              Front-end templates
├─willphp               Framework directory
├─vendor                Composer libraries (optional)
|
├─env.example.env       .env example file
├─composer.json         Composer definition file
├─.gitignore            Git settings
├─LICENSE               License agreement
└─README.md             README file    
```