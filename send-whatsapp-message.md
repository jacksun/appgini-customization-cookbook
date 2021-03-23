# Share a record as a WhatsApp message
## Purpose of this recipe

<img src="https://cdn.bigprof.com/images/whatsapp-logo-md.png" align="right"/>

In this recipe, we're going to add a _**[WhatsApp]**_ button to the detail view form
of one of our tables, allowing us to send the record contents as a WhatsApp message.

This works by simply opening a link to WhatsApp web. The link
is formatted to specify recipient phone number and message to
send. The link _doesn't actually send the message_. It just
puts it in the message box, ready for the user to click _**Send**_.
This is a restriction imposed by WhatsApp to prevent
sending messages by mistake (or by a malicious script) without the user reviewing them first.

For the sake of simplicity, we'll assume that the phone number of the WhatsApp contact
is stored in one of the fields of our table. But you can easily modify this
recipe to prompt the user to enter any other phone number, rather than use the one stored in the record.

## Files to edit
Edit _(or create)_ the file `hooks/tablename-dv.js` (where `tablename` is the name of the
concerned table).

Please make sure to read the comments in the 
code below as they would help you understand and modify the code 
according to how your table and fields are structured.

## Edit 1: Working with mobile and desktop device
## Edit 2: Moving phoneNum and message vars to click function sendWhatsApp because of loading issues

```js
$j(function() {
  
    // prepare 'WhatsApp' button
    $j('<button class="btn btn-success" type="button">Whatsapp</button>')
  
    // send WhatsApp message on clicking the button
    .on('click', function() {
        sendWhatsApp();
    })
  
    // add the button to the form -- above the 'Back' button
    .insertBefore('#deselect');
})

// function to open WhatsApp web, ready to send the specified
// message to the specified phone number
// You probably don't need to edit anything below!
var sendWhatsApp = function() {
    // get the phone number from the current record
    // assuming the phone field is named 'phone'
    // phone number MUST include country code
    var phoneNum = $j('#phone').val();
  
    // our message would be formatted like this:
    // (you should change it depending on which fields
    // you want to send in the WhatsApp message)
    var message = [
        // assuming 'info' is a normal (editable) text field or text area
        'Info: ' + $j('#info').val(),
    
        // assuming 'amount' is a read-only field
        'Amount: ' + $j('#amount').text(),
    
        // assuming 'date' is an editable date field
        // so we're joining the day, month and year drop-downs
        // feel free to change the order depending on the desired date format
        'Date: ' + [
             $j('#date-mm').val(), // month
             $j('#date-dd').val(), // day
             $j('#date').val()     // year
        ].join('/'),               // date separator

        // assuming 'customer' is a lookup drop-down
        $j('#customer-container').select2('data').text
    ].join('\n');
	
	//detect mobile devices and desktops
	if(/Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent)){
		// prepare the WhatsApp- mobile link
		var link = "https://wa.me/" + encodeURIComponent(phoneNum) + "?text=" + encodeURIComponent(message);
	}else{
		// prepare the WhatsApp-web link
		var link = 'https://web.whatsapp.com/send?app_absent=0' +
				   '&phone=' + encodeURIComponent(phoneNum) + 
				   '&text='  + encodeURIComponent(message);
	}

    // open link in a new tab rather than our AppGini app page or directly in WhatsApp App if the device is mobile
    window.open(link);
  
    // returning false prevents submitting the form by mistake
    // on clicking the button
    return false;
}

```
