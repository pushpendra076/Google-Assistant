# Google-Assistant
google assistant integration with servicenow

(function process(/*RESTAPIRequest*/ request, /*RESTAPIResponse*/ response) {

  var writer = response.getStreamWriter();
	hdrs = {};
	var x = request.body.data;
	var inc = '';
	var priority = '';
	var state = '';
	var assigned_to = '';
	var context = '';
	var command = '';
	var message = '';
	var status = '';
	var desc = '';
	var caller = '';
	var content = '';
	var summary = '';
	var comments = '';
	data = {};
	
	if(x.queryResult.intent.displayName =='status_check'){   //status_check is the name of the intent on dialogflow
		
		var numbers = x.queryResult.parameters.number  +''; //from action and parameters of dialogflow
		
		var ci = new GlideRecord('incident');
		ci.addQuery('number','ENDSWITH',numbers);
		
		ci.query();
		while(ci.next()){
			if(ci.assigned_to!='')
				
				assigned_to = ci.getDisplayValue('assigned_to');
			else
				assigned_to = 'no one';
			
			message = 'Incident '+ ci.number+ ' is currently assigned to '+assigned_to+ '. Current State of the incident is '+ ci.getDisplayValue('state');
			
			context='success';
			}
		
	}
	
	var messages = 'Incident ';
	hdrs['Content-Type'] = 'application/json';
	response.setStatus(200);
	response.setHeaders(hdrs);
	var response_body = {
		
		'fulfillmentText': message,
		'payload' : {
			
			"google":{
				
				'expectUserResponse':true,
				'richResponse':{
				
				'items':[
					{
						'simpleResponse':{
							"textToSpeech": message
							
						}
						
					
					}
				]
				
			}
			}
		}
			
		};
			
			writer.writeString(global.JSON.stringify(response_body));
	
	
	
	

})(request, response);
