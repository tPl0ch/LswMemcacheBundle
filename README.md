LswMemcacheBundle
=================

If you want to optimize your web application for high load and/or low load times Memcache is an indispensable tool.
It will manage your session data without doing disk I/O on web or database servers. You can also run it as a
central object storage for your website. In this role it is used for caching expensive API calls or database queries.

This Symfony2 bundle will provide Memcache integration into Symfony2 for session and object storage. It has full
Web Debug Toolbar integration that allows you to analyze and debug the cache behavior and performance.

Screenshot: http://www.leaseweblabs.com/2013/03/memcache-support-in-symfony2-wdt/

### Installation

To install LswMemcacheBundle with Composer just add the following to your 'composer.json' file:

    {
        require: {
            "leaseweb/memcache-bundle": "dev-master",
            ...
        }
    }

The next thing you should do is install the bundle by executing the following command:

    php composer.phar update leaseweb/memcache-bundle

Finally, add the bundle to the registerBundles function of the AppKernel class in the 'app/AppKernel.php' file:

    public function registerBundles()
    {
        $bundles = array(
            ...
            new Lsw\MemcacheBundle\LswMemcacheBundle(),
            ...
        );

Add the following 'handler_id' to your 'app/config/config.yml' file under session:

    framework:
        ...
        session:
            handler_id:  memcache.session_handler

Install the following dependencies (in Debian based systems using 'apt'):

    apt-get install memcached php5-memcached

Do not forget to restart you web server after adding the Memcache module. Now the Memcache
information should show up with a little double arrow (fast-forward) icon in your debug toolbar.

### Usage

When you want to use the cache from the controller you can simply call:
  
    $this->get('memcache')->set('someKey', 'someValue', $timeToLive);
    $this->get('memcache')->get('someKey');
    
The above example shows how to store value 'someValue' under key 'someKey' for a maximum of $timeToLive
seconds. In the second line the value is retrieved from Memcache. If the key can not be found or the
specified number of seconds have passed the 'get' function returns the value 'false'.

### Configuration

If you don't like the default settings of the bundle you can override override these parameters in your config:

    parameters:
        memcache_host: 127.0.0.1
        memcache_port: 11211
        memcache_session_prefix: "session_"
        memcache_session_expire: 3600
        
These settings specify on which host and port the Memcache daemon runs, what prefix should be used for
session data and how long it should store the session data.

### Considerations

LswMemcacheBundle uses the 'memcached' php object (client) not the older 'memcache' php object.
For a comparison of the available clients see: http://code.google.com/p/memcached/wiki/PHPClientComparison

### Known issues

The session write that invokes a memcache set operation is executed after the page has been rendered.
This causes the session writes not to show up in the Web Debug Toolbar. The collect call of the
memcache data collector is executed before the rendering of the page is complete and therefor also
before the session write is executed.
