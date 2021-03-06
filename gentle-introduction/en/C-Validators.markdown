Appendix C - Validators
=======================

Introduction
------------

The symfony form framework comes bundled with a lot of useful validators.
These validators cover the common needs of most projects. This chapter
describes the default form validators bundled with symfony. We have also
included the validators from the `sfPropelPlugin`, and `sfDoctrinePlugin`
plugins, as these plugins are supported by the core team and contain some very
useful validators.

>**TIP**
>Even if you don't use the symfony MVC framework, you can use the validators
>defined in the
>[`sfFormExtraPlugin`](http://svn.symfony-project.com/plugins/sfFormExtraPlugin),
>`sfPropelPlugin`, and `sfDoctrinePlugin` plugins by putting the `validator/`
>directories somewhere in your project.

Before diving into each validator details, let's see what validators have in
common.

### The `sfValidatorBase` Base Class

All symfony validators inherit from the `sfValidator` base class, which
provides some default features available to all validators.

Validators have two goals: cleaning and validating a tainted value.

When creating a validator, you can optionally pass options and error messages
as arguments:

    [php]
    $v = new sfValidatorString(
      array('required' => true),
      array('required' => 'This value is required.')
    );

Options and error messages can also be set by using the `setOptions()` and
`setMessages()` methods:

    [php]
    $v = new sfValidatorString();
    $v->setOptions(array('required' => true));
    $v->setMessages(array('required' => 'This value is required.'));

The `setOption()` and `setMessage()` methods allows to set an individual
option or error message:

    [php]
    $v = new sfValidatorString();
    $v->setOption('required', true);
    $v->setMessage('required', 'This value is required.');

A tainted value can be validated by calling the `clean()` method:

    [php]
    $cleaned = $v->clean('name', 'value', array('class' => 'foo'));

The `clean()` method takes a tainted value as an argument and returns the
cleaned up value. If a validation error occurs, a `sfValidatorError` is
thrown.

>**NOTE**
>Validators are stateless which means that a single validator instance
>can validate as many input values as you want.

The default options defined by `sfValidatorBase` are the following:

| Option        | Error      | Description
| ------------- | ---------- | -----------
| `required`    | `required` | `true` if the value is required, `false` otherwise (`true` by default)
| `trim`        | n/a        | `true` if the value must be trimmed, `false` otherwise (`false` by default)
| `empty_value` | n/a        | empty value to return when the value is not required

The default error messages defined by `sfValidatorBase` are the following:

| Error      | Description
| ---------- | -----------
| `required` | The error message used when the tainted value is empty and required (`Required.` by default).
| `invalid`  | A generic error message when an error occurs (`Invalid.` by default).

You can change the default string used for the `required` and `invalid` error
messages by calling the `setDefaultMessage()` method. These must be set before
any base validators are loaded, for example by using the `setup()` method:

    [php]
    public function setup()
    {
      sfValidatorBase::setDefaultMessage('required', 'This value is required.');
      sfValidatorBase::setDefaultMessage('invalid', 'This value is invalid.');
      parent::setup();
    }

Error messages can contain placeholders. A placeholder is a string enclosed
between `%`. The placeholder are replaced at runtime. All error messages have
access to the tainted value with the `%value%` placeholder. Each error message
can also define specific placeholders.

>**NOTE**
>In the following section, the default `%value%` placeholder is not
>mentioned as it is always available.

Some validators need to know the charset used by the tainted value. By default
the charset is `UTF-8`, but it can be configured by calling the `setCharset()`
method:

    [php]
    sfValidatorBase::setCharset('ISO-8859-1');

>**NOTE**
>If you use the symfony validators with the symfony MVC framework,
>the charset is automatically set according to the charset of
>`settings.yml`.

### Validator Schema

A validator schema is a wrapper validator for one or several other validators.

When an error occurs, a validator schema throws a `sfValidatorErrorSchema`
exception.


In the next sections, the validators have been regrouped into categories.

<div class="pagebreak"></div>

Validators
----------

 * [`sfValidatorString`](B-Validators#chapter_b_sub_sfvalidatorstring)
 * [`sfValidatorRegex`](B-Validators#chapter_b_sub_sfvalidatorregex)
 * [`sfValidatorEmail`](B-Validators#chapter_b_sub_sfvalidatoremail)
 * [`sfValidatorUrl`](B-Validators#chapter_b_sub_sfvalidatorurl)
 * [`sfValidatorInteger`](B-Validators#chapter_b_sub_sfvalidatorinteger)
 * [`sfValidatorNumber`](B-Validators#chapter_b_sub_sfvalidatornumber)
 * [`sfValidatorBoolean`](B-Validators#chapter_b_sub_sfvalidatorboolean)
 * [`sfValidatorChoice`](B-Validators#chapter_b_sub_sfvalidatorchoice)
 * [`sfValidatorPass`](B-Validators#chapter_b_sub_sfvalidatorpass)
 * [`sfValidatorCallback`](B-Validators#chapter_b_sub_sfvalidatorcallback)
 * [`sfValidatorDate`](B-Validators#chapter_b_sub_sfvalidatordate)
 * [`sfValidatorTime`](B-Validators#chapter_b_sub_sfvalidatortime)
 * [`sfValidatorDateTime`](B-Validators#chapter_b_sub_sfvalidatordatetime)
 * [`sfValidatorDateRange`](B-Validators#chapter_b_sub_sfvalidatordaterange)
 * [`sfValidatorFile`](B-Validators#chapter_b_sub_sfvalidatorfile)
 * [`sfValidatorAnd`](B-Validators#chapter_b_sub_sfvalidatorand)
 * [`sfValidatorOr`](B-Validators#chapter_b_sub_sfvalidatoror)
 * [`sfValidatorSchema`](B-Validators#chapter_b_sub_sfvalidatorschema)
 * [`sfValidatorSchemaCompare`](B-Validators#chapter_b_sub_sfvalidatorschemacompare)
 * [`sfValidatorSchemaFilter`](B-Validators#chapter_b_sub_sfvalidatorschemafilter)
 * [`sfValidatorI18nChoiceCountry`](B-Validators#chapter_b_sub_sfvalidatori18nchoicecountry)
 * [`sfValidatorI18nChoiceLanguage`](B-Validators#chapter_b_sub_sfvalidatori18nchoicelanguage)
 * [`sfValidatorI18nTimezone`](B-Validators#chapter_b_sub_sfvalidatori18nchoicetimezone)
 * [`sfValidatorPropelChoice`](B-Validators#chapter_b_sub_sfvalidatorpropelchoice)
 * [`sfValidatorPropelUnique`](B-Validators#chapter_b_sub_sfvalidatorpropelunique)
 * [`sfValidatorDoctrineChoice`](B-Validators#chapter_b_sub_sfvalidatordoctrinechoice)
 * [`sfValidatorDoctrineUnique`](B-Validators#chapter_b_sub_sfvalidatordoctrineunique)

<div class="pagebreak"></div>

Simple Validators
-----------------

### `sfValidatorString`

*Schema validator*: No

The `sfValidatorString` validator validates a string and converts the tainted
value to a string.

| Option       | Error        | Description
| ------------ | ------------ | -----------
| `max_length` | `max_length` | The maximum length of the string
| `min_length` | `min_length` | The minimum length of the string

| Error        | Placeholders | Default Value
| ------------ | ------------ | -------------
| `max_length` | `max_length` | "%value%" is too long (%max_length% characters max).
| `min_length` | `min_length` | "%value%" is too short (%min_length% characters min).

>**CAUTION**
>This validator requires the `mb_string` extension to be installed to
>work correctly. If installed, the string length is computed with the
>`mb_strlen()` function; if not, it uses the `strlen()` function, which
>does not return the real string length if non-ASCII characters are
>present in the string.

### `sfValidatorRegex`

*Schema validator*: No

The `sfValidatorRegex` validator validates a string against a regular
expression.

| Option       | Error     | Description
| ------------ | --------- | -----------
| `pattern`    | `invalid` | A PCRE regex pattern, or an instance of `sfCallable` that returns a regex
| `must_match` | `invalid` | If set to `false`, the regex must not match for the validator to pass

### `sfValidatorEmail`

*Schema validator*: No

The `sfValidatorEmail` validator can validate emails. It inherits from
`sfValidatorRegex`.

### `sfValidatorUrl`

*Schema validator*: No

The `sfValidatorUrl` validator can validate HTTP and FTP URLs. It inherits
from `sfValidatorRegex`.

| Option      | Error     | Description
| ----------- | --------- | -----------
| `protocols` | `invalid` | Protocols to allow. Default: `array('http', 'https', 'ftp', 'ftps')`


### `sfValidatorInteger`

*Schema validator*: No

The `sfValidatorInteger` validator validates an integer and converts the
tainted value to an integer.

| Option | Error | Description
| -----  | ----- | -----------
| `max`  | `max` | The maximum integer to accept
| `min`  | `min` | The minimum integer to accept

| Error | Placeholders | Default Value
| ----- | ------------ | -------------
| `max` | `max`        | "%value%" must be less than %max%.
| `min` | `min`        | "%value%" must be greater than %min%.

The default `invalid` error message is `"%value%" is not an integer.`.

### `sfValidatorNumber`

*Schema validator*: No

The `sfValidatorNumber` validator validates a number (a string that PHP is
able to understand with the [`floatval()`](www.php.net/floatval) function) and
converts the tainted value to a float.

| Option | Error | Description
| -----  | ----- | -----------
| `max`  | `max` | The maximum number to accept
| `min`  | `min` | The minimum number to accept

| Error | Placeholders | Default Value
| ----- | ------------ | -------------
| `max` | `max`        | "%value%" must be less than %max%.
| `min` | `min`        | "%value%" must be greater than %min%.

The default `invalid` error message is `"%value%" is not a number.`.

### `sfValidatorBoolean`

*Schema validator*: No

The `sfValidatorBoolean` validator validates a Boolean and returns either
`true` or `false`.

| Option         | Error | Description
| -------------  | ----- | -----------
| `true_values`  | n/a   | The list of true values (by default: `true`, `t`, `yes`, `y`, `on`, `1`)
| `false_values` | n/a   | The list of false values (by default: `false`, `f`, `no`, `n`, `off`, `0`)

### `sfValidatorChoice`

*Schema validator*: No

The `sfValidatorChoice` validator validates if the tainted value is among a
list of expected values.

| Option     | Error | Description
| ---------- | ----- | -----------
| `choices`  | n/a   | An array of expected values (required)
| `multiple` | n/a   | `true` if the select tag must allow multiple values
| `min`      | n/a   | The minimum number of values that need to be selected (if `multiple` is true)
| `max`      | n/a   | The maximum number of values that need to be selected (if `multiple` is true)

>**NOTE**
>The comparison is done after the tainted value has been casted to a
>string.

### `sfValidatorPass`

*Schema validator*: No

The `sfValidatorPass` validator is a no-op validator, and returns the tainted
value as is.

### `sfValidatorCallback`

*Schema validator*: No

The `sfValidatorCallback` validator delegate the actual validation to a PHP
callback.

The callback is passed the current validator instance, the tainted value and
an array of arguments (from the `arguments` option) as arguments:

    [php]
    function constant_validator_callback($validator, $value, $arguments)
    {
      if ($value != $arguments['constant'])
      {
        throw new sfValidatorError($validator, 'invalid');
      }

      return $value;
    }

    $v = new sfValidatorCallback(array(
      'callback'  => 'constant_validator_callback',
      'arguments' => array('constant' => 'foo'),
    ));

| Option      | Error | Description
| ----------- | ----- | -----------
| `callback`  | n/a   | A valid PHP callback (required)
| `arguments` | n/a   | An array of arguments to pass to the callback

Date Validators
---------------

### `sfValidatorDate`

*Schema validator*: No

The `sfValidatorDate` validates dates and date times (date times are supported
by setting the `with_time` option). Apart from validating the format of a
date, it can enforce a minimum and a maximum valid date.

The validator accepts several type of inputs:

 * an array composed of the following keys: `year`, `month`, `day`, `hour`,
   `minute`, and `second`
 * a string matching the `date_format` regular expression if provided (for
   instance `~(?P<day>\d{2})/(?P<month>\d{2})/(?P<year>\d{4})~`)
 * a string that can be parsed by the `strtotime()` PHP function
 * an integer representing a timestamp

The tainted value is converted to a date by applying the `date_output` or
`datetime_output` format.

| Option                    | Error        | Description
| ------------------------- | ------------ | -----------
| `date_format`             | `bad_format` | A regular expression that dates must match; note that the regular expression must use named subpatterns like `(?P<year>)`
| `with_time`               | n/a          | `true` if the validator must return a time, `false` otherwise
| `date_output`             | n/a          | The format to use when returning a date (default to `Y-m-d`)
| `datetime_output`         | n/a          | The format to use when returning a date with time (default to `Y-m-d H:i:s`)
| `date_format_error`       | n/a          | The date format to use when displaying an error for a `bad_format` error (use `date_format` if not provided)
| `max`                     | `max`        | The maximum date allowed (as a timestamp)
| `min`                     | `min`        | The minimum date allowed (as a timestamp)
| `date_format_range_error` | n/a          | The date format to use when displaying an error for min/max (default to `d/m/Y H:i:s`)

>**NOTE**
>The `date_output` and `datetime_output` options can use any format
>understood by the PHP `date()` function.

| Error        | Placeholders  | Default Value
| ------------ | ------------- | -------------
| `bad_format` | `date_format` | "%value%" does not match the date format (%date_format%).
| `min`        | `min`         | The date must be after %min%.
| `max`        | `max`         | The date must be before %max%.

### `sfValidatorTime`

*Schema validator*: No

The `sfValidatorTime` validates a time.

The validator accepts several type of inputs:

 * an array composed of the following keys: `hour`, `minute`, and `second`
 * a string matching the `time_format` regular expression if provided
 * a string that can be parsed by the `strtotime()` PHP function
 * an integer representing a timestamp

The tainted value is converted to a time by applying the `time_output` format.

| Option              | Error        | Description
| ------------------- | ------------ | -----------
| `time_format`       | `bad_format` | A regular expression that times must match
| `time_output`       | n/a          | The format to use when returning the time (default to `H:i:s`)
| `time_format_error` | n/a          | The format to use when displaying an error for a `bad_format` error (use `date_format` if not provided)

>**NOTE**
>The `time_output` option can use any format understood by the PHP
>`date()` function.

| Error        | Placeholders  | Default Value
| ------------ | ------------- | -------------
| `bad_format` | `time_format` | "%value%" does not match the time format (%time_format%).

### `sfValidatorDateTime`

*Schema validator*: No

The `sfValidatorDateTime` validates dates with a time.

It is a shortcut for:

    [php]
    $v = new sfValidatorDate(array('with_time' => true));

### `sfValidatorDateRange`

*Schema validator*: No

The `sfValidatorDateTime` validates a range of dates.

| Option      | Error     | Description
| ----------- | --------- | -----------
| `from_date` | `invalid` | The from date validator (required)
| `to_date`   | `invalid` | The to date validator (required)

The `from_date` and `to_date` validators must be instances of the
`sfValidatorDate` class.

The `invalid` error message is `"%value%" does not match the time format
(%time_format%).`.

File Validator
--------------

### `sfValidatorFile`

*Schema validator*: No

The `sfValidatorFile` validator validates an uploaded file.

The validator converts the uploaded file to an instance of the
`sfValidatedFile` class, or of the `validated_file_class` option if it is set.

| Option                 | Error        | Description
| ---------------------- | ------------ | -----------
| `max_size`             | `max_size`   | The maximum file size
| `mime_types`           | `mime_types` | Allowed mime types array or category (available categories: `web_images`)
| `mime_type_guessers`   | n/a          | An array of mime type guesser PHP callables (must return the mime type or `null`)
| `mime_categories`      | n/a          | An array of mime type categories (`web_images` is defined by default)
| `path`                 | n/a          | The path where to save the file - as used by the `sfValidatedFile` class (optional)
| `validated_file_class` | n/a          | Name of the class that manages the cleaned uploaded file (optional)

The `web_images` mime-type category contains the following mime-types:

 * `image/jpeg`
 * `image/pjpeg`
 * `image/png`
 * `image/x-png`
 * `image/gif`

If the `mime_types` option is set, the validator need a way to test the
mime-type of the uploaded file. The validator comes bundled with three of
them:

 * `guessFromFileinfo`:        Uses the `finfo_open()` function (from the `Fileinfo` PECL extension)
 * `guessFromMimeContentType`: Uses the `mime_content_type()` function (deprecated)
 * `guessFromFileBinary`:      Uses the file binary (only works on *nix system)

| Error        | Placeholders                  | Default Value
| ------------ | ----------------------------- | -------------
| `max_size`   | `%size%`, `%max_size%`        | File is too large (maximum is %max_size% bytes).
| `mime_types` | `%mime_types%`, `%mime_type%` | Invalid mime type (%mime_type%).
| `partial`    |                               | The uploaded file was only partially uploaded.
| `no_tmp_dir` |                               | Missing a temporary folder.
| `cant_write` |                               | Failed to write file to disk.
| `extension`  |                               | File upload stopped by extension.

The validator maps PHP errors as follows:

  * `UPLOAD_ERR_INI_SIZE`:   `max_size`
  * `UPLOAD_ERR_FORM_SIZE`:  `max_size`
  * `UPLOAD_ERR_PARTIAL`:    `partial`
  * `UPLOAD_ERR_NO_TMP_DIR`: `no_tmp_dir`
  * `UPLOAD_ERR_CANT_WRITE`: `cant_write`
  * `UPLOAD_ERR_EXTENSION`:  `extension`

Logical Validators
------------------

### `sfValidatorAnd`

*Schema validator*: No

The `sfValidatorAnd` validator validates a tainted value if it passes a list
of validators.

The `sfValidatorAnd` constructor takes a list of validators as its first
argument:

    [php]
    $v = new sfValidatorAnd(
      array(
        new sfValidatorString(array('max_length' => 255)),
        new sfValidatorEmail(),
      ),
      array('halt_on_error' => true),
      array('invalid' => 'The input value must be an email with less than 255 characters.')
    );

By default, the validator throws an array of error messages thrown by all the
embedded validators. It can also throw a single error message if the `invalid`
error message is set to a not-empty string, like shown in the above example.

| Option          | Error | Description
| --------------- | ----- | -----------
| `halt_on_error` | `n/a` | Whether to halt on the first error or not (`false` by default)

The order of validators is significant if the `halt_on_error` option is set to
`true`.

The embedded list of validators can also be managed by using the
`getValidators()` and `addValidator()` methods.

### `sfValidatorOr`

*Schema validator*: No

The `sfValidatorOr` validator validates a tainted value if it passes at least
one validator from a list.

The `sfValidatorOr` constructor takes a list of validators as its first
argument:

    [php]
    $v = new sfValidatorOr(
      array(
        new sfValidatorRegex(array('pattern' => '/\.com$/')),
        new sfValidatorEmail(),
      ),
      array(),
      array('invalid' => 'The input value a .com domain or a valid email address.')
    );

By default, the validator throws an array of error messages thrown by all the
embedded validators. It can also throw a single error message if the `invalid`
error message is set to a not-empty string, like shown in the above example.

The embedded list of validators can also be managed by using the
`getValidators()` and `addValidator()` methods.





### `sfValidatorSchema`

*Schema validator*: Yes

The `sfValidatorSchema` validator represents a validator which is composed of
several fields. A field is simply a named validator:

    [php]
    $v = new sfValidatorSchema(array(
      'name'    => new sfValidatorString(),
      'country' => new sfValidatorI18nChoiceCountry(),
    ));

>**NOTE**
>A form is defined by a validator schema of class `sfValidatorSchema`.

This validator only accepts an array as an input value and throws a
`InvalidArgumentException` if this is not the case.

The validator can have a pre-validator, which is executed before all other
validators, and a post-validator, which is executed on the cleaned-up values
after all other validators.

The pre-validator and the post-validator are validator schema themselves that
receive all values. They can be set with the `setPreValidator()` and
`setPostValidator()` methods:

    [php]
    $v->setPostValidator(
      new sfValidatorSchemaCompare('password', '==', 'password_again')
    );

| Option                | Error          | Description
| --------------------- | -------------- | -----------
| `allow_extra_fields`  | `extra_fields` | if `false`, the validator adds an error if extra fields are given in the input array of values (default to `false`)
| `filter_extra_fields` | n/a            | if `true`, the validator filters extra fields from the returned array of cleaned values (default to `true`)

| Error           | Placeholders | Default Value
| --------------- | ------------ | -------------
| `extra_fields`  | `%field%`    | Unexpected extra form field named "%field%".
| `post_max_size` |              | The form submission cannot be processed. It probably means that you have uploaded a file that is too big.

The `sfValidatorSchema` can be used as an array to access the embedded
validators:

    [php]
    $vs = new sfValidatorSchema(array('name' => new sfValidatorString()));

    $nameValidator = $vs['name'];

    unset($vs['name']);

You can access embedded validator schema validators by using the array
notation:

    [php]
    $vs['author']['first_name']->setMessage('invalid', 'The first name is invalid.');

The `post_max_size` error is thrown if the amount of data submitted for a form
exceeds the `post_max_size` value from the `php.ini` file.

### `sfValidatorSchemaCompare`

*Schema validator*: Yes

The `sfValidatorSchemaCompare` validator compares several values from the
given tainted value array:

    [php]
    $v = new sfValidatorSchemaCompare('password', '==', 'password_again');

| Option               | Error | Description
| -------------------- | ----- | -----------
| `left_field`         | n/a   | The left field name
| `operator`           | n/a   | The comparison operator
| `right_field`        | n/a   | The right field name
| `throw_global_error` | n/a   | Whether to throw a global error (`false` by default) or an error tied to the left field

The available operators are the followings:

 * `sfValidatorSchemaCompare::EQUAL` or `==`
 * `sfValidatorSchemaCompare::IDENTICAL` or `===`
 * `sfValidatorSchemaCompare::NOT_EQUAL` or `!=`
 * `sfValidatorSchemaCompare::NOT_IDENTICAL` or `!==`
 * `sfValidatorSchemaCompare::LESS_THAN` or `<`
 * `sfValidatorSchemaCompare::LESS_THAN_EQUAL` or `<=`
 * `sfValidatorSchemaCompare::GREATER_THAN` or `>`
 * `sfValidatorSchemaCompare::GREATER_THAN_EQUAL` or `>=`

By default, the validator throws a global error. If the `throw_global_error`
is set to `true`, an error for the left field is thrown.

The `invalid` error messages accepts the following values: `%left_field%`,
`%right_field%`, and `%operator%`.

### `sfValidatorSchemaFilter`

*Schema validator*: Yes

The `sfValidatorSchemaFilter` validator converts a non-schema validator to a
schema validator. It is sometimes useful in a post validator context:

    [php]
    $v = new sfValidatorSchema();
    $v->setPostValidator(
      new sfValidatorSchemaFilter('email', new sfValidatorEmail())
    );

I18n Validators
---------------

### `sfValidatorI18nChoiceCountry`

*Schema validator*: No

The `sfValidatorI18nChoiceCountry` validates that the tainted value is a valid
country ISO 3166 code.

| Option      | Error     | Description
| ----------- | --------- | -----------
| `countries` | `invalid` | An array of country codes to use (ISO 3166)

### `sfValidatorI18nChoiceLanguage`

*Schema validator*: No

The `sfValidatorI18nChoiceLanguage` validates that the tainted value is a
valid language.

| Option      | Error     | Description
| ----------- | --------- | -----------
| `languages` | `invalid` | An array of languages to use

### `sfValidatorI18nChoiceTimezone`

*Schema validator*: No

The `sfValidatorI18nChoiceTimezone` validates that the tainted value is a
valid timezone.

| Option      | Error     | Description
| ----------- | --------- | -----------
| `languages` | `invalid` | An array of languages to use

Propel Validators
-----------------

### `sfValidatorPropelChoice`

*Schema validator*: No

The `sfValidatorPropelChoice` validator validates that the tainted value is
among the list of records of a given Propel model.

The list of records can be restricted by using the `criteria` option.

The tainted value must be the primary key of records. This can be changed by
passing the `column` option.

| Option       | Error | Description
| ------------ | ----- | -----------
| `model`      | n/a   | The model class (required)
| `criteria`   | n/a   | A criteria to use when retrieving objects
| `column`     | n/a   | The column name (`null` by default which means the primary key is used) - must be in field name format
| `connection` | n/a   | The Propel connection to use (`null` by default)
| `multiple`   | n/a   | `true` if the select tag must allow multiple selections

>**NOTE**
>This validator does not work for model with a composite primary key.

### `sfValidatorPropelUnique`

*Schema validator*: Yes

The `sfValidatorPropelUnique` validator validates the uniqueness of a
column or a group of columns (`column` option) for a Propel model.

If the uniqueness is on several columns, the error can be thrown globally by
setting the `throw_global_error` option.

| Option               | Error | Description
| -------------------- | ----- | -----------
| `model`              | n/a   | The model class (required)
| `column`             | n/a   | The unique column name in Propel field name format (required). If the uniquess is for several columns, you can pass an array of field names
| `field`              | n/a   | Field name used by the form, other than the column name
| `primary_key`        | n/a   | The primary key column name in Propel field name format (optional, will be introspected if not provided). You can also pass an array if the table has several primary keys
| `connection`         | n/a   | The Propel connection to use (`null` by default)
| `throw_global_error` | n/a   | Whether to throw a global error (`false` by default) or an error tied to the first field related to the column option array

Doctrine Validators
-------------------

### `sfValidatorDoctrineChoice`

*Schema validator*: No

The `sfValidatorDoctrineChoice` validator validates that the tainted value is
among the list of records of a given Doctrine model.

The list of records can be restricted by using the `query` option.

The tainted value must be the primary key of records. This can be changed by
passing the `column` option.

| Option       | Error | Description
| ------------ | ----- | -----------
| `model`      | n/a   | The model class (required)
| `alias`      | n/a   | The alias of the root component used in the query
| `query`      | n/a   | A query to use when retrieving objects
| `column`     | n/a   | The column name (`null` by default which means the primary key is used) - must be in field name format
| `connection` | n/a   | The Doctrine connection to use (`null` by default)

>**NOTE**
>This validator does not work for model with a composite primary key.

### `sfValidatorDoctrineUnique`

*Schema validator*: Yes

The `sfValidatorDoctrineUnique` validator validates the uniqueness of a
column or a group of columns (`column` option) for a Doctrine model.

If the uniqueness is on several columns, the error can be thrown globally by
setting the `throw_global_error` option.

| Option               | Error | Description
| -------------------- | ----- | -----------
| `model`              | n/a   | The model class (required)
| `column`             | n/a   | The unique column name in Doctrine field name format (required). If the uniquess is for several columns, you can pass an array of field names
| `primary_key`        | n/a   | The primary key column name in Doctrine field name format (optional, will be introspected if not provided). You can also pass an array if the table has several primary keys
| `connection`         | n/a   | The Doctrine connection to use (`null` by default)
| `throw_global_error` | n/a   | Whether to throw a global error (`false` by default) or an error tied to the first field related to the column option array
