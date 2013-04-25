title=HandlebarsJS: How to iterate over a map.
subtitle=Template Engine HandlebarsJS: iterator
created=2013-04-26T01:15:44
tags=JavaScript, JS, Handlebar, Handlebars, Iterator
language=en

Handlebars has the build in iterator `each` to iterate over a list.  
To iterate over a map you have to implement your own `registerHelper`. A possible solution could look like the following example:

Definition of new Handlebar function:

	Handlebars.registerHelper('eachMapEntries', function(context, options) {	    
	    var ret = "";
	    $.each(context, function(key, value) {
	        var entry = {"key": key, "value": value};
	        ret = ret + options.fn(entry);
	    });
	    return ret;
	});

Template:

	<script id="entry-template" type="text/x-handlebars-template">
	    {{#eachMapEntries data}}
	        key: {{this.key}} / value: {{this.value}} <br>
	    {{/eachMapEntries}}
	    <hr>
	</script>


Template controller:

	var source = $("#entry-template").html();
    var template = Handlebars.compile(source);

	//JSON data structure - payload
    var data = {"key1": "value1", "key2": "value2"};

    var container = {};
    container.data = data;
	//data2 as a dummy value for a flexible datastructure
    container.data2 = data2; 
    
    var html = template(container);
    $('#content').html(html);

