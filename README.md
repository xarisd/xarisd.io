xarisd.io
==========================

You can enjoy it online at <http://xarisd.io>

## About this repository

Here you can find the source code that drives the site.

The site is made with [Jekyll](http://jekyllrb.com/) and [rake](https://github.com/ruby/rake) for automation awesomeness.

## How to build/test the site

1. You will need **Ruby 2.1.2**

2. Install Bundler
<pre><code>gem install bundler</code></pre>

3. Install the required gems (Ruby libraries needed)
<pre><code>bundle install</code></pre>

4. Test the site:
  * Run the jekyll server locally with <code>rake serve</code> in your console (Ctrl + C to stop it)
  * Open <code>http://localhost:4000</code> in your browser
  * And enjoy!

5. Build the site for deployment
  <pre><code>rake</code></pre>

6. Deploy the site somewhere via FTP (the poor man's way)
  * You will need access to an FTP server
  * Create a <code>credentials.yaml</code> by copying the <code>credentials.example.yaml</code> and put your credentials (don't worry it is git ignored).
  * Run <pre><code>rake production deploy</code></pre>
  * And you are done!
  * If something goes wrong, you can always do it by hand: Just FTP-upload the contents of the <code>_deploy</code> folder to the proper location on your web server.

7. Explore what else you can do with <code>rake</code> tasks (clean, show, copy and deploy presentations, etc)
<pre><code>rake -T</code></pre>

## Tools used

* [Jekyll: Simple, blog-aware, static sites](http://jekyllrb.com/)
* [rake: A make-like build utility for Ruby](https://github.com/ruby/rake)
