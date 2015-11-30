.. _authentification:

Authentication
=================

Principles
-------------------

Some of the data published by the Data Services requires an authorization. In order to get one, you must create an account on the platform at http://data.grandlyon.com/creation-de-compte/ and specify the different datasets and access manner you require. 

Once this stage is completed, you'll get a login (generally the e-mail address used when creating the account) and a password. These are strictly personal, and they must be used, in the context of application development for third parties,  with some wariness. 

But let's first see how to use these authentication elements to reach the restricted datasets.

The authentication method is `Basic Auth HTTP <https://en.wikipedia.org/wiki/Basic_access_authentication>`_ At the time of access to each kind of service, it is thus mandatory to use the 'Authorization' HTTP header, in which will be inserted in your login and password, separated by a colon (":") and  `base64 <https://en.wikipedia.org/wiki/Base64>`_ encoded. The header should then look like this :

::

  Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==
 
But don't be fooled! Base64 encoding is not an encryption! The process is reversible and one can retrieve the encoded values very easily. Coupled with an HTTP stream, which encrypts the information sent by and towards the server, they are not decipherable, but written without any precaution in the code. 


cURL and WGET examples
--------------------------

The usage of an authorization header with cURL is very easy. Let's imagine a user with the following credentials:

* login : demo
* password : demo4dev

The cURL instruction to use in order to access the "demo.demovelov" data on the data service would then be:

::

    cURL -u demo:demo4dev curl https://download.data.grandlyon.com/ws/rdata/demo.demovelov/all.json?compact=false

If there is no mistake, you should then get a json stream. 

The WGET instruction is almost the same: 

:: 

    wget --http-user=demo --http-password=demo4dev https://download.data.grandlyon.com/ws/rdata/demo.demovelov/all.json?compact=false
 

PHP and Python examples
---------------------------

Whether in PHP or Python, the libraries used to emit HTTP requests all include the ability of declaring the HTTP authorization header: 

For Python and urllib2 we'll have:

.. code-block:: python

    import urllib2, base64
    
    # set basic information
    username = 'demo'
    password = 'demo4dev'
    url = 'https://download.data.grandlyon.com/ws/rdata/demo.demovelov/all.json'
    
    # prepare the request Object
    request = urllib2.Request(url)
    
    # encode the username / password couple into a single base64 string
    base64string = base64.encodestring('%s:%s' % (username, password)))
    
    # then add this string into the Authorization header
    request.add_header("Authorization", "Basic %s" % base64string)
    
    # and open the url
    result = urllib2.urlopen(request)
    
    # then handle the result the way you like

In PHP, we'll use the cURL library included:

.. code-block:: php

    <?php

    // set basic information
    $username='demo';
    $password='demo4dev';
    $URL='https://download.data.grandlyon.com/ws/rdata/demo.demovelov/all.json';
    
    // instantiate a new cUrl object
    $ch = curl_init();
    
    // Everything is an option with PHPCurl !
    curl_setopt($ch, CURLOPT_URL,$URL);
    
    // set RETURNTRANSFER to true to be able to handle the result
    curl_setopt($ch, CURLOPT_RETURNTRANSFER,1);
    
    // set this option for enabling Basic Auth HTTP rules
    curl_setopt($ch, CURLOPT_HTTPAUTH, CURLAUTH_BASIC);
    
    // the previous setting will help here to encode the username/password into the correct format
    curl_setopt($ch, CURLOPT_USERPWD, "$username:$password");
    
    // and lift off...
    $result=curl_exec ($ch);
    
    // then handle the result the way you like
    
    ?>
