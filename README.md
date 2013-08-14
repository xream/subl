# subl:// URL handler

A subl:// _URL handler_ for **Sublime Text 2** and **Sublime Text 3** on Mac OSX. Made _very_ quickly in AppleScript.

Follows the syntax of the [TextMate URL scheme](http://blog.macromates.com/2007/the-textmate-url-scheme/).

## Installation

- Download and unzip [Subl.app.zip](https://github.com/dhoulb/subl/releases/download/v1.1/Subl.app.zip)
- Move Subl.app to your **/Applications** directory
- Open once to register the handler

## How to use

Generate URLs that point to:
`subl://open?url=file://{{file}}&line={{line}}&column={{column}}`

Examples:
- `subl://open?url=file:///path/to/file.php&line=12&column=4`
- `subl://open?url=file:///path/to/other.php&line=192`

### Args

- `url` - Absolute file:// link to the file, e.g. `file:///path/to/file.txt`
- `line` - Line number in the file, e.g. `192`
- `column` - Column number in the file, e.g. `14`
- `path` - Path to Sublime Text, e.g. `/Applications/Sublime Text.app`

### Path

The default path to Sublime Text is `/Applications/Sublime Text.app`. To change it, specify the `path` URL parameter. Ensure that any spaces are URL encoded as `%20`.

Example:
`subl://open?url=file://file.php&line=20&path=/Applications/Sublime%20Text%203.app`

## subl:// URLs with PHP errors

This code snippet will re-route all PHP errors to a custom error handler, and turn filenames into clicky Sublime Text links.

```php
// Custom error handler function.
// Turns filenames into clicky subl:// links (requires http://github.com/dhoulb/subl)
// Also cleanses the ugly part of pathnames, so "/long/path/to/file/name.php on line 7" becomes "name.php - 7"
function subl_handler($level, $error, $file, $line)
{
	// Check if error reporting is off.
	if ($level === 0) return;

	// Is there a file?
	if ($file)
	{
		// Ugly part to strip off the path name.
		$strip = dirname($_SERVER['SCRIPT_FILENAME']) . DIRECTORY_SEPARATOR;

		// Change $file to a clicky link.
		$file = '<strong><a href="subl://open?url=file://' . $file . '&line=' . $line . '">'
		. str_replace($strip, '', $file)
		. ' - ' . $line
		. '</a></strong>';
	}

	// Output the file and the error.
	echo '<pre class="error">';
	if ($file) echo $file . '<br>';
	echo $error;
	echo '</pre>';
}

// Additional function to enable handling for fatal errors too.
function subl_shutdown() { if ($error = error_get_last()) subl_handler($error['type'], $error['message'], $error['file'], $error['line']); }

// Register handlers.
set_error_handler('subl_handler', E_ALL); // Register custom function as the error handler.
register_shutdown_function('subl_shutdown'); // Register the custom shutdown function.
ini_set('display_errors', false); // Turn off built-in errors.
```

_Remember:_ It's recommended to show no errors at all on live production websites. PHP errors leak details than can be useful to attackers.

## Comments

Hopefully some day a URL scheme will be baked in to Sublime Text. For now, this’ll do.