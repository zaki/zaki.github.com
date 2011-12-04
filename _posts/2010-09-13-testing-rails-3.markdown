---
author: zaki
date: '2010-09-13 16:26:14'
layout: post
type: code
slug: testing-rails-3
status: publish
title: Testing Rails 3.0
wordpress_id: '369'
tags:
  - rails
  - ruby
---

## Getting rails 3

Rails 3.0 was released this week (*), so I thought I'd go and try to run it's
test suite - on windows.

The first step is to get Ruby 1.9.2p0 from the [windows installer
page](http://rubyinstaller.org/) and install it. I'm running on
[pik](http://github.com/vertiginous/pik), so after installation and setup, I'd
switch to 1.9.2 with

    pik sw 192

The stock 1.9.2 install contains a slightly old version (2.5.8) of the rdoc
gem, so the first thing to do is to update it with _gem update_.

Another tool that is necessary is the bundler gem, so install it with _gem
install bundler_.  Now to fetch all the prerequisites, all we need to do is
run _bundle install_ in the root of the rails source. (Note the command is
bundle, the gem name is bundler - I sometimes forget this when typing fast)

## Testing

What I wanted to do is to run the built-in test suite. Theoretically, just
running _rake test_ in the root should work  and it would test every
component, but in practice that doesn't quite work out that well (lots of
warnings push the results out of screen pretty fast), so I opted to run the
tests for each component separately.

### activemodel

In the activemodel directory, entering rake test will run the test suite for
the activemodel component only. It's result should be just a little bit messy
with a lot of warnings, but in the end, we can get our first easy victory:

    400 tests, 1110 assertions, 0 failures, 0 errors, 0 skips

### activeresource

While this pack spews a huge number of warnings, the result is quite tame and
we can more or less trust activeresource on windows.

    272 tests, 789 assertions, 0 failures, 0 errors, 0 skips

### actionmailer

Another easy victory in spite of some warnings.

    175 tests, 479 assertions, 0 failures, 0 errors, 0 skips

### activesupport

After running rake test for activesupport, the picture is slightly different,
but still not unreasonable (these tests were run without memcached). What I
found was

    2055 tests, 9169 assertions, 35 failures, 1 errors, 0 skips

Let's see what these failures are. There are basically two areas of
activesupport that do not pass on my windows machine. One is reasonable, the
other one is slightly more difficult to understand: File operations
(especially permissions) and Time. I've uploaded the exact output in [this
gist](http://gist.github.com/558529).

To dig a little deeper I looked at a few of the time errors.

    3) Failure: test_current_returns_time_zone_today_when_zone_default_set
    (DateExtCalculationsTest) [test/core_ext/date_ext_test.rb:397]:
    <Sat, 01 Jan 2000> expected but was <Fri, 31 Dec 1999>.

The code that is responsible for the testing is the following:

{%highlight ruby%}
def test_current_returns_time_zone_today_when_zone_default_set
  Time.zone_default = ActiveSupport::TimeZone['Eastern Time (US & Canada)']
  with_env_tz 'US/Central' do
    Time.stubs(:now).returns Time.local(1999, 12, 31, 23)
    assert_equal Date.new(1999, 12, 31), Date.today
    assert_equal Date.new(2000, 1, 1), Date.current
  end
ensure
  Time.zone_default = nil
end
{% endhighlight %}

Or in other words, 1999-12-31 23:00 in Central Time should be 2000-01-01 00:00
in Eastern Time. The difference between _Date.today_ and _Data.current_ is
that _Date.current_ is supposed to take the time zone into account.

{%highlight ruby%}
def current
  ::Time.zone_default ? ::Time.zone.today : ::Date.today
end
{%endhighlight%}

So why doesn't it work on Windows? The answer lies in the with_env_tz method,
which looks like this:

{%highlight ruby%}
def with_env_tz(new_tz = 'US/Eastern')
  old_tz, ENV['TZ'] = ENV['TZ'], new_tz
  yield
ensure
  old_tz ? ENV['TZ'] = old_tz : ENV.delete('TZ')
end
{%endhighlight%}

This method sets the time zone to be used through an environment variable TZ.
However on windows, this variable can only be set at script startup and not
during runtime (its value will be ignored). In fact, this is the method that
causes all the failing tests on windows. This shouldn't actually cause a
problem for rails projects, though, the timezone is properly set up at
startup.

A more interesting part is the exception raised in one of the tests. That one
is coming from a test that tries to silence STDERR, which does not work on
windows and raises Errno::EBADF, which is unfortunately not caught. A way to
deal with it would be to catch this exception.

{%highlight ruby%}
def test_silence_stderr
  old_stderr_position = STDERR.tell
  silence_stderr { STDERR.puts 'hello world' }
  assert_equal old_stderr_position, STDERR.tell
rescue Errno::ESPIPE, Errno::EBADF
  # Skip if we can't STDERR.tell
end
{%endhighlight%}

Moving on to the other filesystem-related errors, we can find that they are
related to permissions. For instance the following test

{%highlight ruby%}
def test_atomic_write_preserves_default_file_permissions
  contents = "Atomic Text"
  File.atomic_write(file_name, Dir.pwd) do |file|
    file.write(contents)
    assert !File.exist?(file_name)
  end
  assert File.exist?(file_name)
  assert_equal 0100666 ^ File.umask, file_mode
  assert_equal contents, File.read(file_name)
ensure
  File.unlink(file_name)
rescue
  nil
end
{%endhighlight%}

is testing if the file's permissions are kept as the default (0666 -rw-rw-
rw-), however here's what the ruby documentations has to say about File.open()

> Opens the file named by fileName according to aModeString (default is ``r'')
and returns a new File object. See Table 22.5 on page 326 for a description of
aModeString. The file mode may optionally be specified as a Fixnum by or-ing
together the flags described in Table 22.3 on page 305. Optional permission
bits may be given in aPermNum. **These mode and permission bits are platform
dependent**; on Unix systems, see open(2) for details.

The general meaning of "platform dependent" seems to be "it doesn't work on
Windows".

The default permission on Windows seems to be  0644, so in this case, the test
itself could account for the differences in the platform and we could fix the
test by writing something like the following:

    DEFAULT_PERMISSIONS = Config::CONFIG[‘host_os’] =~ /mswin|mingw/ ? 0100644 : 0100666

<div class="alert-message block-message info">
  Update: Thanks for Luis Lavena to point out the flaws in the original platform checking
</div>

Although this will not help with the tests that check if permissions can be
set properly, at least it solves the default permission test. At this point
there is only one final type of tests that fail, the FileStoreTests. It would
probably be proper to skip these permission tests on windows altogether.

The remaining failures come from FileStoreTest in test/caching_test.rb:

    test_crazy_key_characters(FileStoreTest) <3> expected but was <nil>.
    test_decrement(FileStoreTest) <2> expected but was <nil>.
    test_increment(FileStoreTest) <2> expected but was <nil>.
    test_local_cache_of_decrement(FileStoreTest) <2> expected but was <3>.
    test_local_cache_of_increment(FileStoreTest) <3> expected but was <2>.

It seems that actually all tests fail on the increment or decrement methods,
so let's try to investigate which feature might be "platform dependent" this
time.

{%highlight ruby%}
def increment(name, amount = 1, options = nil)
  file_name = key_file_path(namespaced_key(name, options))
  lock_file(file_name) do
    options = merged_options(options)
    if num = read(name, options)
      num = num.to_i + amount
      write(name, num, options)
      num
    else
      nil
    end
  end
end

def lock_file(file_name, &block) # :nodoc:
  if File.exist?(file_name)
    File.open(file_name, 'r') do |f|
      begin
        f.flock File::LOCK_EX
        yield
      ensure
        f.flock File::LOCK_UN
      end
    end
  else
    yield
  end
end
{%endhighlight%}

As it can be guessed, the ruby documentation has this to say about File.flock:
"Not available on all platforms." In fact the problem happens at read (or more
precisely at read_entry) where a permission denied exception is raised, but is
promptly swallowed and nil is returned instead. This makes increment and
decrement to fail.

The bottom line about using activesupport on windows is that timezone-changing
via environment variables will not work on the fly. Also, there is the problem
with file-based caching that can easily lead to frustration, but otherwise
activesupport can be trusted.

### actionpack

Nice clean output for the most part and the result is also pretty nice:

    2819 tests, 10698 assertions, 2 failures, 5 errors, 0 skips

    1) Failure: test_caching_stylesheet_link_tag_when_caching_on(AssetTagHelperTest)
    <55> expected but was <59>.

The failure is a difference in the response size and its expected size. That
means that there is a 4 byte excess. And of course those bytes are coming from
the CRLFs.

### activerecord

Now, ActiveRecord's output is really messy on windows. In fact to the point
that tests don't even run and all I'm left in the end is:

    Errors running test_mysql, test_mysql2, test_sqlite3, test_postgresql

(*) And because this post already takes a lot longer than I wanted, I'll take
a look at ActiveRecord in another post, and will conclude this one.

