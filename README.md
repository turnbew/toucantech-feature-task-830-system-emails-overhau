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

ADD EXTRA TASK: 1462 [
					"Automatic thank you emails for online donations added to system emails: ""Dear {{ first_name }}, 
					Thank you for your donation of {{ donation_amount }} on {{ payment_date_recieved }}. 
					{{ site_name }}"" - triggered after someone makes a donation on
					.../supportus, .../fundraising/donation and any fundraising donation widgets, etc"
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
	
		\network-site\addons\default\modules\network_settings\css\email_center.css
		
			ADDED CODE: 
			
				tr.email-templates-email-groups {
					min-height: 30px;
					background: #f5f5f5;
				}

				tr.email-templates-email-groups td {
					min-height: 30px;
					font-weight: bold;
					color: #16629D;
					padding-top: 7px !important;
					padding-bottom: 7px !important;
				}
		
	
	
		\network-site\addons\default\modules\network_settings\views\email_center\tables\templates.php
		
			CHANGED CODE: 
			
				FROM: 
				
					<?php foreach ($templates as $template): ?>
						<?php if(!$template->is_default and !in_array($template->slug,$ignore_list)): ?>
							<tr>
								<td><?php echo $template->name ?></td>
								<td><?php echo $template->description ?></td>
								<td class="actions">
									<div class="pull-right">
										<?php echo anchor('admin-portal/email-center/system-emails/preview/' . $template->id, 'Preview', 'data-toggle="modal" data-target="#system-email-preview" class="btn btn-primary"') ?>
										<?php echo anchor('admin-portal/email-center/system-emails/edit/' . $template->id, 'Edit', 'class="btn btn-primary"') ?>
									</div>
								</td>
							</tr>
						<?php endif ?>
					<?php endforeach ?>
				
				TO: 
				
					<?php array_multisort(array_column($templates, 'email_group'), SORT_ASC, array_column($templates, 'name'), SORT_ASC, $templates); ?>
					<?php if( count($templates) > 0 ): ?> 
						<?php $i = 0; $newGroup = true; ?>
						<?php foreach ($templates as $template) : ?>
							<?php if ( ! $template->is_default and !in_array($template->slug,$ignore_list)) { ?>
								<?php if ($newGroup) : ?>
									<tr class="email-templates-email-groups"><td colspan="6"><?php echo $template->email_group ?></td></tr>
								<?php endif; ?>
								<tr>
									<td><?php echo $template->name ?></td>
									<td><?php echo $template->description ?></td>
									<td><?php echo $template->sent_out_to ?></td>
									<td><?php echo $template->active ? lang('global:yes') : lang('global:no') ?></td>
									<td><?php echo lang('system_emails:options:frequency:label:' . $template->frequency ) ?></td>
									<td class="actions">
										<div class="pull-right">
											<?php echo anchor('admin-portal/email-center/system-emails/preview/' . $template->id, 'Preview', 'data-toggle="modal" data-target="#system-email-preview" class="btn btn-primary"') ?>
											<?php echo anchor('admin-portal/email-center/system-emails/edit/' . $template->id, 'Edit', 'class="btn btn-primary"') ?>
										</div>
									</td>	
								</tr>
								<?php
									$newGroup = ( isset($templates[($i + 1)]->email_group) and $template->email_group != $templates[($i + 1)]->email_group ); 
									$i++; 
								?>
							<?php } else { 
								$i++;
							} ?>
						<?php endforeach ?>
					<?php endif; ?>
					
					
					

		\network-site\addons\default\modules\fundraising\controllers\fundraising.php
	
			CHANGED CODE: 
			
				Inside function fundraising
				
					.....
					$msg =  $status = $donorsEmail = '';
					....
					$donorsEmail = $this->current_user ? $this->current_user->email : $data['profile']->email;
					....
					'user_email'                => $donorsEmail,
					....
					//Sending thank e-mail to the person who made the donation (donor)
					if ($status == 'ok' and $donorsEmail != '') {	
						$emailsData = array(
							'slug' => 'fundraising_thank_donation',
							'to' => 'lajos.d.05@gmail.com',
							'first_name' => $donor_details['first_name'],
							'donation_amount' =>  $donation['payment_amount'] . " " . $donation['payment_currency'],
							'payment_date_recieved' => date('Y-m-d H:i'),
							'site_name' => Settings::get('site_name'),
						);
						Events::trigger('email', $emailsData, 'array');
					}
					....
					
					
	
	
		\network-site\system\codeigniter\libraries\Email.php
		
			Lots of Changes - original file is preserved for a while

		
		
		\network-site\addons\default\modules\bbusers\libraries\user_fill_out_profile_notification_lib.php
		
			CNAHGED CODE 
			
				Inside function generate_emails
					
					....
					#getting template details
					$increaseNetworkTemplate = $this->ci->email_templates_m->get_templates('increase_network_opportunities');
					....
					if ( count($increaseNetworkTemplate) > 0 ) {
						set_time_limit(3600);
						$before = microtime(true);
						
						if ($increaseNetworkTemplate['en']->frequency_enabled) {
							switch ($increaseNetworkTemplate['en']->frequency) {
								case "daily":
										$date_since = date('U', strtotime("-1 days"));
									break;
								case "weekly":
										$date_since = date('U', strtotime("-7 days"));
									break;	
								case "bi_weekly":
										$date_since = date('U', strtotime("-14 days"));
									break;
								case "monthly":
										$date_since = date('U', strtotime("-1 month"));
									break;
								case "no_frequency":
										$date_since = date('U', strtotime("-60 days"));
									break;
								default:
										$date_since = date('U', strtotime("-60 days"));
							}
						}
						else {
							$date_since = date('U', strtotime("-60 days"));
						}
				....
				}//END if ($clubNewsletterTemplate['en']->active)
				.....
				
				
		
		
		\network-site\addons\default\modules\clubs\events.php
		
			CNAHGED CODE 
			
				Inside function send_newsletter 
				
				..... 
				$clubNewsletterTemplate = $this->ci->email_templates_m->get_templates('club_newsletter');
				.....
				if ( count($clubNewsletterTemplate) > 0 ) {	
				....
				#setting date limit
				$date_limit = new DateTime();
				if ($clubNewsletterTemplate['en']->frequency_enabled) {
					switch ($clubNewsletterTemplate['en']->frequency) {
						case "daily":
								$date_limit->sub(new DateInterval('P1D'));
							break;
						case "bi_weekly":
								$date_limit->sub(new DateInterval('P14D'));
							break;
						case "monthly":
								$date_limit->sub(new DateInterval('P1M'));
							break;
						case "days_60":
								$date_limit->sub(new DateInterval('P60D'));
							break;
						case "days_90":
								$date_limit->sub(new DateInterval('P90D'));
							break;
						case "no_frequency": 
						case "weekly":
								$date_limit->sub(new DateInterval('P7D'));
							break;
					}
				} else {
					$date_limit->sub(new DateInterval('P7D'));
				}
				.....
				} //END if ($clubNewsletterTemplate['en']->active)
			
		
		
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
			Removed UPDATES from HERE to 
		\network-site\addons\default\modules\messaging\details.php
		
			ADDED CODE: 
		
				inside install function: 
				
					$this->_addColumnsEmailTemplatesTable();
					$this->db->add_boolean_field_to_table($this->db->dbprefix('email_templates'), 'frequency_enabled', $null = false, $default = 0, $after = '');
					$this->db->add_boolean_field_to_table($this->db->dbprefix('email_templates'), 'active', $null = false, $default = 1, $after = ''); 
					$this->_updateEmailTemplatesTable(array('frequency_enabled', 'frequency', 'club_newsletter_body', 'sent_out_to', 'email_group', 'name', 'description'));
					$this->_insertEmailTemplatesTable();	
				
				
				inside upgrade function:
				
					//Updating - add two new coloumns to email_templates table
					if (version_compare($old_version, '2.0.3', 'lt')) {
						$this->_addColumnsEmailTemplatesTable();
						$this->db->add_boolean_field_to_table($this->db->dbprefix('email_templates'), 'frequency_enabled', $null = false, $default = 0, $after = '');
						$this->db->add_boolean_field_to_table($this->db->dbprefix('email_templates'), 'active', $null = false, $default = 1, $after = ''); 
						$this->_updateEmailTemplatesTable(array('frequency_enabled', 'frequency', 'club_newsletter_body', 'sent_out_to', 'email_group', 'name', 'description'));
						$this->_insertEmailTemplatesTable();	
					}
					
				added new function 
				
					private function _insertEmailTemplatesTable() 
					{
					   $table = $this->db->dbprefix('email_templates');
					   
					   //body is base64 decoded: online encoder - decoder: https://www.base64encode.org/
					   if ( ! $this->db->value_exists('fundraising_thank_donation', 'slug', $table)) {
						   $this->db->insert($table, array(
								'slug' => 'fundraising_thank_donation',
								'name' => 'Thank donation e-mail',
								'description' => 'Thank e-mail after donation received',
								'subject' => 'Thank you for your donation!',
								'body' => base64_decode('PHA+RGVhciB7e2ZpcnN0X25hbWV9fSw8L3A+DQo8cD5UaGFuayB5b3UgZm9yIHlvdXIgZG9uYXRpb24gb2Yge3sgZG9uYXRpb25fYW1vdW50IH19IG9uIHt7IHBheW1lbnRfZGF0ZV9yZWNpZXZlZCB9fS48L3A+DQo8cD57e3NpdGVfbmFtZX19PC9wPg0K'),
								'lang' => 'en',
							));
					   }
					} //END function _insertEmailTemplatesTable
				   

				   
					private function _addColumnsEmailTemplatesTable() 
					{
						$table = $this->db->dbprefix('email_templates');
						
						if ( ! $this->db->field_exists('frequency', $table)) {
							$sql = 	"ALTER TABLE  " . $table . " ADD COLUMN frequency VARCHAR(20) NOT NULL DEFAULT 'no_frequency'";
							$this->db->query($sql);
						}
						
						if ( ! $this->db->field_exists('sent_out_to', $table)) {
							$sql = 	"ALTER TABLE " . $table . " ADD COLUMN sent_out_to VARCHAR(25) NOT NULL ";
							$this->db->query($sql);
						}
						
						if ( ! $this->db->field_exists('email_group', $table)) {
							$sql = 	"ALTER TABLE " . $table . " ADD COLUMN email_group VARCHAR(25) NOT NULL ";
							$this->db->query($sql);
						}
					} //END function _addColumnsEmailTemplatesTable



					
					private function _updateEmailTemplatesTable($updateFields = array()) 
					{
						$table = $this->db->dbprefix('email_templates');

						#update frequency_enabled field
						if ( in_array('frequency_enabled', $updateFields) ) {
							$this->db->set('frequency_enabled', '1') 
										->where('slug', 'club_newsletter')->or_where('slug','increase_network_opportunities')
										->update($table);
						}
						
						#update frequency field
						if ( in_array('frequency', $updateFields) ) {
							//frequency options can be found here: \network_settings\language\english\system_emails_lang.php
							//frequency options field is built from language variables and not from DB
							$this->db->set('frequency', 'weekly')->where('slug', 'club_newsletter')->update($table);
							$this->db->set('frequency', 'days_60')->where('slug', 'increase_network_opportunities')->update($table);			
						}
						
						#update weekly_newsletter body 
						if ( in_array('club_newsletter_body', $updateFields) ) {
							$hit = $this->db->select('id')
												->where('slug', 'club_newsletter')
												->get($table)
												->row_array();
						
							 //body is base64 decoded: online encoder - decoder: https://www.base64encode.org/
							if ( ! empty($hit)) {
								$value_new = base64_decode('PCEtLSBGdWxsIGJvZHkgb2YgbmV3c2xldHRlciAtIE91dGVyIHRhYmxlIC0tPg0KPHRhYmxlIHN0eWxlPSJ3aWR0aDogMTAwJTsgaGVpZ2h0OiAxMDAlOyBtYXJnaW46IDBweDsgcGFkZGluZzogMHB4O2JhY2tncm91bmQtY29sb3I6ICNmZmZmZmY7IGJvcmRlcjogbm9uZTsiIGJvcmRlcj0iMCIgY2VsbHNwYWNpbmc9IjAiPg0KCTx0ciBzdHlsZT0id2lkdGg6IDEwMCU7IGhlaWdodDogMTAwJTsiPg0KCQk8dGQgc3R5bGU9IndpZHRoOiAxMDAlOyBoZWlnaHQ6IDEwMCU7IHRleHQtYWxpZ246IGNlbnRlcjsgcGFkZGluZy10b3A6IDZweDsgcGFkZGluZy1ib3R0b206IDIwcHg7IiBhbGlnbj0iY2VudGVyIj4NCgkJCQkJDQoJCQk8IS0tIEZ1bGwgY29udGVudCBvZiBuZXdzbGV0dGVyIC0tPg0KCQkJPHRhYmxlIHN0eWxlPSJ3aWR0aDogMTAwJTsgbWF4LXdpZHRoOiA2NDBweDsgbWFyZ2luLWxlZnQ6IGF1dG87IG1hcmdpbi1yaWdodDogYXV0bzsgbWFyZ2luOiBhdXRvOyBoZWlnaHQ6IDEwMCU7IHBhZGRpbmc6IDBweDsgYmFja2dyb3VuZC1jb2xvcjogI2ZmZmZmZjsgYm9yZGVyOiBub25lOyIgYm9yZGVyPSIwIiBjZWxsc3BhY2luZz0iMCIgY2VsbHBhZGRpbmc9IjUiPg0KCQkJCQ0KCQkJCTwhLS0gYmFubmVyIC0tPg0KCQkJCXt7IGVtYWlsX2Jhbm5lciB9fQ0KCQkJCQ0KCQkJCTx0cj48dGQgc3R5bGU9ImhlaWdodDogMjBweDsiPiZuYnNwOzwvdGQ+
								PC90cj4NCgkJCQkNCgkJCQk8IS0tIHRpdGxlIC0tPg0KCQkJCTx0cj4gICAgDQoJCQkJCTx0ZCBhbGlnbj0ibGVmdCI+
								DQoJCQkJCQk8aDIgc3R5bGU9IndpZHRoOiAxMDAlOyB0ZXh0LWFsaWduOiBsZWZ0OyBmb250LWZhbWlseTogdmVyZGFuYSxnZW5ldmE7IGNvbG9yOiMzNjM2MzY7IGZvbnQtc2l6ZTogMTZweDsgbGluZS1oZWlnaHQ6IDE4cHg7IG1hcmdpbjogMHB4OyBwYWRkaW5nOiAwcHg7Ij4NCgkJCQkJCQlBbiB1cGRhdGUgZW1haWwgZnJvbSB7eyBjbHViX25hbWUgfX0NCgkJCQkJCTwvaDI+
								DQoJCQkJCTwvdGQ+
								DQoJCQkJPC90cj4NCgkJCQkNCgkJCQk8dHI+
								PHRkIHN0eWxlPSJoZWlnaHQ6IDIwcHg7Ij4mbmJzcDs8L3RkPjwvdHI+
								DQoJCQkJDQoJCQkJPCEtLSBpbnRybyB0ZXh0IC0tPg0KCQkJCTx0cj4gICAgDQoJCQkJCTx0ZCBhbGlnbj0ianVzdGlmeSI+
								DQoJCQkJCQk8cCBzdHlsZT0iZm9udC1mYW1pbHk6IHZlcmRhbmEsZ2VuZXZhOyBmb250LXNpemU6IDEzcHg7IGxpbmUtaGVpZ2h0OiAxNXB4OyB0ZXh0LWFsaWduOiBqdXN0aWZ5OyI+
								DQoJCQkJCQkJPHNwYW4gc3R5bGU9ImZvbnQtd2VpZ2h0OiBub3JtYWw7Ij4NCgkJCQkJCQkJPHNwYW4+
								RGVhciBGSVJTVF9BTkRfTEFTVF9OQU1FX0NIQU5HRV9KVVNUX0FUX1NFTkRJTkcsPC9zcGFuPiANCgkJCQkJCQkJPGJyLz48YnIvPg0KCQkJCQkJCQlIZXJlIGlzIHlvdXIgd2Vla2x5IGRpZ2VzdCBmcm9tIHRoZSB7eyBjbHViX25hbWUgfX0gcGFnZSBvbiB7eyBjb21tdW5pdHlfbmFtZSB9fS4NCgkJCQkJCQkJPGJyIC8+
								PGJyLz4NCgkJCQkJCQkJSWYgeW91IHdvdWxkIGxpa2UgdG8gc2hhcmUgc29tZXRoaW5nIHdpdGgge3sgY2x1Yl9uYW1lIH19IHBsZWFzZSBjbGljaw0KCQkJCQkJCTwvc3Bhbj4gPGEgaHJlZj0ie3sgY2x1Yl9saW5rIH19IiA+
								aGVyZTwvYT4uDQoJCQkJCQk8L3A+
								DQoJCQkJCTwvdGQ+
								DQoJCQkJPC90cj4NCgkJCQkNCgkJCQkNCgkJCQl7eyBuZXdOZXdzU25pcHBldCB9fQ0KCQkJCQ0KCQkJCXt7IG5ld0Fubm91Y2VtZW50c1NuaXBwZXQgfX0NCgkJCQkNCgkJCQl7eyBuZXdNZW1iZXJzU25pcHBldCB9fQ0KCQkJCQ0KCQkJPC90YWJsZT48IS0tIEVORCBGdWxsIGNvbnRlbnQgb2YgbmV3c2xldHRlciAtLT4NCgkJCQ0KCQk8L3RkPg0KCTwvdHI+
								DQo8L3RhYmxlPjwhLS0gRU5EIEZ1bGwgYm9keSBvZiBuZXdzbGV0dGVyIC0tPg0K');
								
								$this->db->where('id', $hit['id'])
												->set('body', $value_new)
												->update($table);
							}
						}#END update weekly_newsletter body 
						
						#update sent_out_to field
						if ( in_array('sent_out_to', $updateFields) or 
							 in_array('email_group', $updateFields) or 
							 in_array('name', $updateFields) or  
							 in_array('description', $updateFields)
						) {
							$updateValues = array(
								'activation' => array('sent_out_to' => 'Member', 'email_group' => 'Account management', 'name' => ''),
								'admin_added_member_notification' => array('sent_out_to' => 'Admin', 'email_group' => 'Account management', 'name' => ''),
								'club_newsletter' => array('sent_out_to' => 'Member', 'email_group' => 'Clubs', 'name' => ''),
								'comment_on_commented_news_article' => array('sent_out_to' => 'Member', 'email_group' => 'Networking', 'name' => ''),
								'comment_on_commented_reply_article' => array('sent_out_to' => 'Member', 'email_group' => 'Networking', 'name' => ''),
								'comments_on_story_notification' => array('sent_out_to' => 'Member', 'email_group' => 'Networking', 'name' => ''),
								'comments' => array('sent_out_to' => 'comments', 'email_group' => 'Networking', 'name' => ''),
								'connection_request_accepted' => array('sent_out_to' => 'Member', 'email_group' => 'Networking', 'name' => ''),
								'connection_request' => array('sent_out_to' => 'Member', 'email_group' => 'Networking', 'name' => ''),
								'contact' => array('sent_out_to' => 'Member', 'email_group' => 'Account management', 'name' => ''),
								'event_registration_confirmation' => array('sent_out_to' => 'Member', 'email_group' => 'Event', 'name' => ''),
								'family_member_added' => array('sent_out_to' => 'Member', 'email_group' => 'Networking', 'name' => ''),
								'family_member_invited' => array('sent_out_to' => 'Member', 'email_group' => 'Networking', 'name' => ''),
								'forgotten_password' => array('sent_out_to' => 'Member', 'email_group' => 'Account management', 'name' => ''),
								'fundraising_discuss' => array('sent_out_to' => 'Admin', 'email_group' => 'Fundraising', 'name' => ''),
								'fundraising_new_donation' => array('sent_out_to' => 'Admin', 'email_group' => 'Fundraising', 'name' => 'New fundraising donation'),
								'fundraising_thank_donation' => array('sent_out_to' => 'Member', 'email_group' => 'Fundraising', 'name' => 'Donation thank-you email'),
								'increase_network_opportunities' => array('sent_out_to' => 'Member', 'email_group' => 'Networking', 'name' => ''),
								'institution_added' => array('sent_out_to' => 'Admin', 'email_group' => 'Networking', 'name' => ''),
								'invites_google_email' => array('sent_out_to' => 'New member', 'email_group' => 'Networking', 'name' => 'Invites engine / Google'),
								'job_application' => array('sent_out_to' => 'Member', 'email_group' => 'Job', 'name' => ''),
								'job_new' => array('sent_out_to' => 'Member', 'email_group' => 'Job', 'name' => ''),
								'jobs_alerts_admin_summary' => array('sent_out_to' => 'Member', 'email_group' => 'Job', 'name' => ''),
								'jobs_alerts' => array('sent_out_to' => 'Member', 'email_group' => 'Job', 'name' => ''),
								'lead_created_notification' => array('sent_out_to' => 'Admin', 'email_group' => 'Clubs', 'name' => ''),
								'message_flagged_spam' => array('sent_out_to' => 'Admin', 'email_group' => 'Networking', 'name' => ''),
								'message_marked_spam' => array('sent_out_to' => 'Admin', 'email_group' => 'Networking', 'name' => ''),
								'messaging_email' => array('sent_out_to' => 'Member', 'email_group' => 'Networking', 'name' => ''),
								'new_club_member_notification' => array('sent_out_to' => 'Admin', 'email_group' => 'Clubs', 'name' => 'A new member has joined the club and requires approval'),
								'new_club_member' => array('sent_out_to' => 'Member', 'email_group' => 'Clubs', 'name' => 'New member joins club member is affiliated with'),
								'new_club' => array('sent_out_to' => 'Admin', 'email_group' => 'Clubs', 'name' => ''),
								'new_password' => array('sent_out_to' => 'Member', 'email_group' => 'Account management', 'name' => ''),
								'new_user_approved' => array('sent_out_to' => 'Member', 'email_group' => 'Account management', 'name' => ''),
								'new_user_rejected' => array('sent_out_to' => 'Member', 'email_group' => 'Account management', 'name' => ''),
								'new_user_welcome' => array('sent_out_to' => 'Member', 'email_group' => 'Account management', 'name' => 'New member welcome message'),
								'newsletters_subscriber_activation' => array('sent_out_to' => 'Admin', 'email_group' => 'News', 'name' => ''),
								'order-complete-admin' => array('sent_out_to' => 'Admin', 'email_group' => 'Shop', 'name' => ''),
								'order-complete-user' => array('sent_out_to' => 'Member', 'email_group' => 'Shop', 'name' => ''),
								'order-dispatched' => array('sent_out_to' => 'Member', 'email_group' => 'Shop', 'name' => ''),
								'profile_school_education_notification' => array('sent_out_to' => 'Admin', 'email_group' => 'Clubs', 'name' => ''),
								'quick_member_email' => array('sent_out_to' => 'Member', 'email_group' => 'Account management', 'name' => '', 'description' => 'Quick emails sent from the record page'),
								'registered' => array('sent_out_to' => 'Admin', 'email_group' => 'Networking', 'name' => ''),
								'school_autoreply' => array('sent_out_to' => 'Member', 'email_group' => 'School', 'name' => ''),
								'school_interest' => array('sent_out_to' => 'Admin', 'email_group' => 'School', 'name' => ''),
								'school_tagged' => array('sent_out_to' => 'Member', 'email_group' => 'News', 'name' => ''),
								'subscriber_activated' => array('sent_out_to' => 'Member', 'email_group' => 'Networking', 'name' => ''),
								'user_blocked' => array('sent_out_to' => 'Member', 'email_group' => 'Account management', 'name' => ''),
								'user_club_story_requires_approval' => array('sent_out_to' => 'Club admin', 'email_group' => 'Clubs', 'name' => ''),
								'user_nolonger_in_flexigroup' => array('sent_out_to' => 'Admin', 'email_group' => 'Account management', 'name' => ''),
								'user_school_tagged_in_news_article' => array('sent_out_to' => 'Member', 'email_group' => 'News', 'name' => ''),
								'user_story_approved' => array('sent_out_to' => 'Admin', 'email_group' => 'News', 'name' => ''),
								'user_story_rejected' => array('sent_out_to' => 'Admin', 'email_group' => 'News', 'name' => ''),
								'user_story_requires_approval' => array('sent_out_to' => 'Admin', 'email_group' => 'News', 'name' => ''),
								'user_story_submitted' => array('sent_out_to' => 'Admin', 'email_group' => 'News', 'name' => ''),
								'user_tagged_in_news_article' => array('sent_out_to' => 'Member', 'email_group' => 'News', 'name' => 'Member tagged in news article'),
							);

							if ( in_array('sent_out_to', $updateFields) ) 
								foreach ($updateValues as $slug => $values)
									$this->db->set('sent_out_to', $values['sent_out_to'])->where('slug', $slug)->update($table);
									
							if ( in_array('email_group', $updateFields) ) 
								foreach ($updateValues as $slug => $values)
									$this->db->set('email_group', $values['email_group'])->where('slug', $slug)->update($table);
									
							if ( in_array('name', $updateFields) ) 
								foreach ($updateValues as $slug => $values)
									if ( isset($values['name']) AND $values['name'] != '' ) 
										$this->db->set('name', $values['name'])->where('slug', $slug)->update($table);
									
							if ( in_array('description', $updateFields) ) 
								foreach ($updateValues as $slug => $values)
									if ( isset($values['description']) AND $values['description'] != '' ) 
										$this->db->set('description', $values['description'])->where('slug', $slug)->update($table);
						}#end update sent_out_to, email_group, name, description fields
						
					} //END function _updateEmailTemplatesTable
	
		
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
			
