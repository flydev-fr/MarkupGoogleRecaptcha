# MarkupGoogleRecaptcha
Google reCAPTCHA for ProcessWire.
This module simply adds reCAPTCHA to your form.

# How To Install
1. Download the [zip file](https://github.com/flydev-fr/MarkupGoogleRecaptcha/archive/master.zip) at Github or clone directly the repo into your `site/modules`
2. If you dowloaded the `zip file`, extract it in your `sites/modules` directory
3. Goto the modules admin page, click on refresh and install it.

# API
You must create an API key prior to use this module. Goto [https://www.google.com/recaptcha/admin](https://www.google.com/recaptcha/admin) to create your own. Next, add the API keys information to the module's settings.

![module settings](http://i.imgur.com/wVeEvTn.png)

# Usage
1. Call the module : `$captcha = $modules->get("MarkupGoogleRecaptcha");`
2. Call `$captcha->getScript();` somewhere to get the javascript used by reCAPTCHA
3. Render reCAPTCHA in a standard HTML `<form></form>` by calling `$captcha->render()`
or
Render reCAPTCHA in an `InputfieldForm` by passing as argument your form to the render function: `$captcha->render($form)`
4. Call `verifyResponse()` to get the result. It return `TRUE` if the challenge was successfull.

# Example
### We make a form using ProcessWire's API and inject the reCaptcha :
```php
$out = '';

$captcha = $modules->get("MarkupGoogleRecaptcha");
// if submitted, check response
if ($captcha->verifyResponse() === true)
{
	$out .= "Hi " . $input->post["name"] . ", thanks for submitting the form!";
} 
else 
{
	$form = $modules->get("InputfieldForm");
	$form->action = $page->url;
	$form->method = "post";
	$form->attr("id+name", "form");

	$field = $this->modules->get('InputfieldText');
	$field->name = "name";
	$field->placeholder = "name";
	$form->add($field);
	
	// CAPTCHA - our form as argument, the function will add an InputfieldMarkup to our form
	$captcha->render($form);
	
	// add a submit button
	$submit = $this->modules->get("InputfieldSubmit");
	$submit->name = "submit";
	$submit->value = 'Submit';
	$form->add($submit);
	
	$out .= $form->render();
	
	$out .= $captcha->getScript();

}

echo $out;
```

### A plain HTML form :
```php
$captcha = $modules->get("MarkupGoogleRecaptcha");
// if submitted check response
if ($captcha->verifyResponse() === true) 
{
	$out .= "Hi " . $input->post["name"] . ", thanks for submitting the form!";
} 
else 
{
	$out .= "<form method='post' action='{$page->url}'>\n"
	     . "\t<input type='text' name='name'>\n"
	     . $captcha->render() // render reCaptcha
	     . "\t<input type='submit'>\n"
	     . "</form>\n";
	     
	$out .= $captcha->getScript();
}

echo $out;
```
