TASK DATE (HARD - medium): 17.03.2018 - FINISHED: 03.04.2018 (for first test)

TASK SHORT DESCRIPTION: 830 [
								" Ability to toggle system emails on/off and edit the frequency
								* Add new 'Tags' to pull data into emails/system emails:
								{{site_name}}
								{{institution_name}}
								{{year_to}} default_profile_question_answers default_profile_question_answers_offline
								{{class_of}} default_profiles - if empty/null use calculated_class_of - http://school.networkbecause.local/admin-portal/members/edit/130 tab: offline/record
								{{first_name}} that pulls the preferred_name for a user. If preferred name is NULL, pull the first_name of the user.
								{{last_name}} default_profiles
								* New system email to capture opt ins (for those unspecified) - turned off as default"
							]

							//Set frequency at this letters
							Increase your networking opportunities	
							Weekly newsletter
							
GITHUB REPOSITORY CODE: feature/task-830-system-emails-overhaul

ORIGINAL WORK: https://github.com/BusinessBecause/network-site/tree/feature/task-830-system-emails-overhaul

CHANGES
 
	NEW FILES: 
	
		\network-site\addons\default\modules\network_settings\language\english\system_emails_lang.php
		\network-site\addons\default\modules\network_settings\views\email_center\templates\edit_frequency_field.php
 
	IN FILES: 

		\network-site\system\codeigniter\libraries\Email.php
		
			Lots of Changes - original file is preserved for a while

		
		
		C:\toucantech.dev\network-site\addons\default\modules\clubs\events.php
		
		
		
		\network-site\addons\shared_addons\modules\pyroforms\models\pyroforms_m.php

			
			CHANGE CODE I.
				
				Inside function send_email()

					FROM: $body    = $this->parser->parse_string($body, $form_data, true);
					TO: $body    = $this->parser->parse_string($body, $form_data, $return = true, $is_include = false, $streams_parse = array(), $noEmptyReplacement = true);



		\network-site\addons\shared_addons\modules\newsletters\events.php

			CHANGE CODE I.
				
				Inside function process_queue()

					FROM: $message = $this->CI->parser->parse_string($html, $data, true);
					TO: $message = $this->CI->parser->parse_string($html, $data, $return = true, $is_include = false, $streams_parse = array(), $noEmptyReplacement = true);




		\network-site\addons\shared_addons\modules\newsletters\controllers\admin\newsletters.php

			CHANGE CODE I.
				
				Inside function sendtest($id = false)
				Inside function spamtest($id = false)

					FROM: $html = $this->parser->parse_string($html, $data, true);
					TO: $html = $this->parser->parse_string($html, $data, $return = true, $is_include = false, $streams_parse = array(), $noEmptyReplacement = true);




		\network-site\addons\default\modules\network_settings\events.php

			CHANGE CODE I.

				Inside function process_queue()

					FROM: $$message = $this->parser->parse_string($html, $data, true);
					TO: $$message = $this->parser->parse_string($html, $data, $return = true, $is_include = false, $streams_parse = array(), $noEmptyReplacement = true);


		
		\network-site\addons\default\modules\network_settings\controllers\email_center.php

			CHANGE CODE I.
				
				Inside function sendtest($id = false)
				Inside function debugtest($id = false)

					FROM: $html = $this->parser->parse_string($html, $data, true);
					TO: $html = $this->parser->parse_string($html, $data, $return = true, $is_include = false, $streams_parse = array(), $noEmptyReplacement = true);

		\network-site\system\cms\libraries\Lex\Parser.php

			CHANGED CODE I. 

				inside function parse_string and paramters list of function as well 

					FROM: 	parse_string($string, $data = array(), $return = false, $is_include = false, $streams_parse = array())
					TO: 	parse_string($string, $data = array(), $return = false, $is_include = false, $streams_parse = array(), $noEmptyReplacement = false)
					....
					FROM: 	$text = $this->parse_callback_tags($text, $data, $callback);
					TO: 	$text = $this->parse_callback_tags($text, $data, $callback, $noEmptyReplacement);
		
			CHANGED CODE II. 

				inside function parse_variables and paramters list of function as well 

					FROM: 	parse_variables($text, $data, $callback = null, $noEmptyReplacement = false)
					TO: 	parse_variables($text, $data, $callback = null)
					....
					FROM: 	$str = $this->parse_variables($str, $item_data, $callback);
					TO:		$str = $this->parse_variables($str, $item_data, $callback, $noEmptyReplacement);
					....
					FROM: 	$str = $this->parse_callback_tags($str, $item_data, $callback);
					TO:		$str = $this->parse_callback_tags($str, $item_data, $callback, $noEmptyReplacement);

			CHANGED CODE III. 

				inside function parse_callback_tags and paramters list of function as well 

					FROM: 	parse_callback_tags($text, $data, $callback)
					TO: 	parse_callback_tags($text, $data, $callback, $noEmptyReplacement = false)
					....
					ADDED CODE: 	

						if ($in_condition)
						{
							$replacement = $this->value_to_literal($replacement);
						}
						/* BEGINNING HERE */
						#we preserve variables if do ask empty values replacements - changing { and } chars to special ones to avoid parsing
						if ($replacement == '' and $noEmptyReplacement) {
							$replacement = str_replace(array('{', '}'), array('_left_braces_', '_right_braces_'), $tag); 
							$replacement = str_replace(' ', '', $replacement); 	
						}
						/* ENDING HERE */
						$text = preg_replace('/'.preg_quote($tag, '/').'/m', addcslashes($replacement, '\\$'), $text, 1);
						.....
						#we preserve variables if do ask empty values replacements - changing special ones to { and } chars
						if ($noEmptyReplacement) $text = str_replace(array('_left_braces_', '_right_braces_'), array('{', '}'), $text); 




		\network-site\system\cms\libraries\MY_Parser.php

			CHANGED CODE I. 

				inside function parse_string and paramters list of function as well 

					FROM: 	parse_string($string, $data = array(), $return = false, $is_include = false, $streams_parse = array())
					TO: 	parse_string($string, $data = array(), $return = false, $is_include = false, $streams_parse = array(), $noEmptyReplacement = false)
					....
					FROM: 	return $this->_parse($string, $data, $return, $is_include, $streams_parse);
					TO: 	return $this->_parse($string, $data, $return, $is_include, $streams_parse, $noEmptyReplacement);
		
			CHANGED CODE II. 

				inside function _parse and paramters list of function as well 

					FROM: 	_parse($string, $data, $return = false, $is_include = false, $streams_parse = array())
					TO: 	_parse($string, $data, $return = false, $is_include = false, $streams_parse = array(), $noEmptyReplacement = false)
					....
					FROM: 	$parsed = $parser->parse($string, $data, array($this, 'parser_callback'), $allow_php = false);
					TO:		$parsed = $parser->parse($string, $data, array($this, 'parser_callback'), $allow_php = false, $noEmptyReplacement);



		\network-site\system\cms\modules\templates\events.php

			CHANGED CODE: 

				Inside function send_email

					FROM: $body = $this->ci->parser->parse_string($body, $data, true);

					TO: $body = $this->ci->parser->parse_string($body, $data, $return = true, $is_include = false, $streams_parse = array(), $noEmptyReplacement = true);


	
		\network-site\system\cms\modules\templates\models\email_templates_m.php
		
			CHANGED CODE: 
				
				inside function get_many_by 
				
					FROM: $results = parent::get_many_by('slug', $slug);
					
					TO:  $results = parent::get_many_by('slug = "' . $slug . '" AND active = 1'); 
	
			ADDED NEW FUNCTION:
			
				 /**
				 * Is Active
				 */
				public function is_active($slug = '', $lang = 'en')
				{
					if ($slug == '') {
						return true;
					} else {			
						return parent::count_by(array(
							'lang'		=> $lang, 
							'slug'		=> $slug,
							'active >'	=> 0,
						)) > 0;
					}
				}
	
	
	
		
		\network-site\addons\default\modules\network_settings\views\email_center\templates\edit.php
		
			ADDED CODE:
			
				<li class="<?php echo alternator('even', '') ?>">
					<label for="active">
						<?=lang('system_emails:label:active')?>?
						<a class="my-tool-tip" data-toggle="tooltip" data-placement="right" title="" data-original-title="<?=lang('system_emails:info:active')?>"> 
							<span class="glyphicon glyphicon-question-sign" style="color: #999;"></span>
						</a>
						<div class="tooltip fade right in" style="top: -12px; left: 12px; display: none;">
							<div class="tooltip-arrow"></div>
							<div class="tooltip-inner"></div>
						</div>
					</label>
					<div class="input">
						<input type="checkbox" name="active" id="active" style="min-width: 30px; height: 30px; margin: 0px;" value="<?=$email_template->active?>"<?=$email_template->active_checked?>>
					</div>
				</li>											
				
				<?=$frequencyInputField?>
				
				
		
		
		\network-site\addons\default\modules\network_settings\controllers\email_templates.php
		
			ADDED CODE: 
			
				inside edit function: 
				
					....
					$lang = $this->lang->load('system_emails');
					....
					//getting FREQUENCY options from Lang variables - NOT FROM DB!!! - file: network_settings\language\english\system_emails_lang.php
					$frequencyInputField = '';
					if ( $email_template->frequency_enabled ) {
						$frequencySelectList = '';
						$frequencyOptions = array();
						foreach($lang as $key => $value) {
							if( strpos($key, "system_emails:options:frequency:") !== false ) {
								$frequencyOptions[str_replace("system_emails:options:frequency:", "", $key)] = $value;
							}
						}
						$frequencySelectList = form_dropdown('frequency', $frequencyOptions, $email_template->frequency, 'id="frequency" style="min-width: 200px;"');
						$frequencyInputField = $this->load->view('email_center/templates/edit_frequency_field', array('frequencySelectList' => $frequencySelectList), true);
					}
					....
					'frequency' => $this->input->post('frequency'),
					'active' => $this->input->post('active') == "" ? 0 : 1,
					....
					//Set active checkbox options status
					$email_template->active_checked = ($email_template->active) ? " checked" : "";
					....
					->set('frequencyInputField', $frequencyInputField)
					....
					
					
		
		\network-site\addons\default\modules\network_settings\details.php
		
			ADDED CODE: 
		
				inside install function: 
				
					$$this->_addFrequencyColoumnToEmailTemplatesTable();
					$this->db->add_boolean_field_to_table($this->db->dbprefix('email_templates'), 'frequency_enabled', $null = false, $default = 0, $after = '');
					$this->_updateFrequencyEnabledFieldInEmailTemplatesTable();
					$this->db->add_boolean_field_to_table($this->db->dbprefix('email_templates'), 'active', $null = false, $default = 1, $after = ''); 
				
				
				inside upgrade function:
				
					//Updating - add two new coloumns to email_templates table
					if (version_compare($old_version, '2.0.89', 'lt')) {
						$this->_addFrequencyColoumnToEmailTemplatesTable();
						$this->db->add_boolean_field_to_table($this->db->dbprefix('email_templates'), 'frequency_enabled', $null = false, $default = 0, $after = '');
						$this->_updateFrequencyEnabledFieldInEmailTemplatesTable();
						$this->db->add_boolean_field_to_table($this->db->dbprefix('email_templates'), 'active', $null = false, $default = 1, $after = ''); 
					}
					
				added new function 
				
					protected function _addFrequencyColoumnToEmailTemplatesTable() 
					{
						$table = $this->db->dbprefix('email_templates');
						
						if ( ! $this->db->field_exists('frequency', $table)) {
							$sql = 	"ALTER TABLE  " . $table . 
									"	ADD COLUMN frequency VARCHAR(20) NOT NULL DEFAULT 'no_frequency'";
							
							if ( ! $this->db->query($sql)) return false;
						}
					}//END function _addFrequencyColoumnToEmailTemplatesTable
					
					private function _updateFrequencyEnabledFieldInEmailTemplatesTable() 
				   {
						return $this->db->set('frequency_enabled', '1') 
										->where('slug', 'club_newsletter')->or_where('slug','increase_network_opportunities')
										->update($this->db->dbprefix('email_templates'));
				   }
		
TEST 
	
	search for e-mail sending: ->email->send();

	CHANGES in \network-site\system\codeigniter\libraries\Email.php file

		put the next code inside function personalization - end of the last step

			/* JUST FOR TEST */	 		echo "email: " . $email . "<br>Personalization fields and values:<br>";
			/* JUST FOR TEST */			echo "<ul>";
			/* JUST FOR TEST */			foreach($parseValues as $key => $value) {
			/* JUST FOR TEST */				echo "<li>key: " . $key . " - value: " . $value . "</li>";
			/* JUST FOR TEST */			}
			/* JUST FOR TEST */			echo "</ul>";
			/* JUST FOR TEST */			echo "Letter's body: <br><br>" . $this->_finalbody . "<br><br>--------------------------------<br><br>";		

	CHANGES in Controller: \network-site\addons\default\modules\network_settings\controllers\email_templates.php
		
		inside function index adding next code front of the function
					
			/* ORIG			public function index()	 */	 
			/* TEST */		public function index($emailTemplateSlug = '', $email = '') 
					{	
				
			/* TEST beginning */	
			if ($emailTemplateSlug != '') {
				if ($emailTemplateSlug == 'club_newsletter') {
					echo "Weekly newsletters test.<br>"; 
					echo "You need to change commit, news ...etc, because changes activate sending newsletter.<br><br>";
					echo "<STRONG>IMPORTANT!!!!</STRONG> BEFORE MERGE, WE NEED TO TAKE OUT THIS TEST MODE!<br><br>";	
					require_once __DIR__ . '/../../clubs/events.php';		
					$events_obj = new Events_clubs();					
					$events_obj->send_newsletter();
				}
				else if ($emailTemplateSlug == 'increase_network_opportunities') {
					echo "InCrease network opportunities newsletters test.<br>"; 
					echo "<STRONG>IMPORTANT!!!!</STRONG> BEFORE MERGE, WE NEED TO TAKE OUT THIS TEST MODE!<br><br>";
					require_once __DIR__ . '/../../bbusers/libraries/user_fill_out_profile_notification_lib.php';	
					$obj = new User_fill_out_profile_notification_lib();					
					$obj->generate_emails();
				}
				else {
					echo "Simple e-mail sending.<br><br>"; 
					echo "Creating link:  http://school.networkbecause.local/admin-portal/email-center/system-emails/index/<email_template_slug>/<email_address1 .. use _at_ insted of @>-<email_address2 .. use _at_ insted of @> ...-<email_addressN .. use _at_ insted of @><br><br>";
					echo "Sample link:  http://school.networkbecause.local/admin-portal/email-center/system-emails/index/comments/lajos_at_toucantech.com-lajos.d.05_at_gmail.com.<br><br>";
					echo "<STRONG>IMPORTANT!!!!</STRONG> BEFORE MERGE, WE NEED TO TAKE OUT THIS TEST MODE!<br><br>";
					Events::trigger(
						'email', 
						array(
							'slug' => $emailTemplateSlug,
							'to' => ($email == '') ? Settings::get('contact_email') : explode('-', str_replace('_at_', '@', $email)),
						), 
						'array'
					);
				}

				exit;
			}	
			/* TEST ending */
	
	CHANGES in DB - default_profiles_table 
	
		user_id = 1 - from: info@pelicanconnect.com  - to: lajos@toucantech.com
		user_id = 1 - from: sian@toucantech.com  - to: lajos.d.05@gmail.com
	
	TEST URLs on local machine: 
		Normal letter sending, after action: http://school.networkbecause.local/admin-portal/email-center/system-emails/index/comments/lajos_at_toucantech.com-lajos.d.05_at_gmail.com
		Weekly newsletter: http://school.networkbecause.local/admin-portal/email-center/system-emails/index/club_newsletter
		Increase opportunity: http://school.networkbecause.local/admin-portal/email-center/system-emails/index/increase_network_opportunities

	TEST URLs on test server 14
		Normal letter sending, after action: http://test14.toucantech.com/admin-portal/email-center/system-emails/index/comments/lajos_at_toucantech.com-lajos.d.05_at_gmail.com
		Weekly newsletter: http://test14.toucantech.com/admin-portal/email-center/system-emails/index/club_newsletter
		Increase opportunity: http://test14.toucantech.com/admin-portal/email-center/system-emails/index/increase_network_opportunities
	
	IMPORTANT TO KNOW: 

		- FILE where e-mail sending is registered: \network-site\system\cms\modules\templates\events.php 
				 Events::register('email', array($this, 'send_email'));

- Available parse keys: 

	key: institution_name - value: Toucan School
	key: site_name - value: Online Community
	key: display_name - value: Toucan Tech
	key: first_name - value: Toucan // = preferred_name if it's not empty
	key: last_name - value: Tech
	key: gender - value:
	key: website - value: http://www.toucantech.com
	key: summary - value: Beautiful Community Software!
	key: location_city - value: London
	key: location_country - value: Cayman Islands
	key: was_in_countries - value: United Kingdom United States of America
	key: nationality - value: British
	key: achievements - value: Setting up beautifully simple networks for schools
	key: dob - value:
	key: preferred_first_name - value:
	key: class_of - value: 2031
	key: year_from - value:
	key: year_to - value: 2026
	key: year_group_to_id - value: 2
	key: positions_of_responsibility - value:
	key: responsibilities - value:
	key: connection_to_school - value: Built this alumni networking platform!
	
	
	TEST e-mail templates
	
		<h5>TEST for personalization</h5>
		<br><br>
		<p>Personalization keys: </p>
		<ul>
		<li>institution_name - value: {{ institution_name }}</li>
		<li>site_name - value: {{ site_name }}</li>
		<li>display_name - value: {{ display_name }}</li>
		<li>first_name - value: {{ first_name }}</li>
		<li>last_name - value: {{ last_name }}</li>
		<li>gender - value: {{ gender }}</li>
		<li>website - value: {{ website }}</li>
		<li>summary - value: {{ summary }}</li>
		<li>location_city - value: {{ location_city }}</li>
		<li>location_country - value: {{ location_country }}</li>
		<li>was_in_countries - value: {{ was_in_countries }}</li>
		<li>nationality - value: {{ nationality }}</li>
		<li>achievements - value: {{ achievements }}</li>
		<li>dob - value: {{ dob }</li>
		<li>preferred_first_name - value: {{ preferred_first_name }}</li>
		<li>class_of - value: {{ class_of }}</li>
		<li>year_from - value: {{ year_from }</li>
		<li>year_to - value: {{ year_to }}</li>
		<li>year_group_to_id - value: {{ year_group_to_id }}</li>
		<li>positions_of_responsibility - value: {{ positions_of_responsibility }}</li>
		<li>responsibilities - value: {{ responsibilities }}</li>
		<li>connection_to_school - value: {{ connection_to_school }}</li>
		</ul>

END TEST
			
