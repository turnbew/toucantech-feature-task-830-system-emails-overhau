FOR PRIVACY AND CODE PROTECTING REASONS THIS IS A SIMPLIFIED VERSION OF CHANGES AND NEW FEATURES

TASK DATE (HARD - medium): 17.03.2018 - FINISHED: 03.04.2018 (for first test)

TASK LEVEL: HARD

TASK SHORT DESCRIPTION: [ " Ability to toggle system emails on/off and edit the frequency * Add new 'Tags' to pull data into emails/system emails: {{site_name}} {{institution_name}} {{year_to}} default_profile_question_answers default_profile_question_answers_offline {{class_of}} default_profiles - if empty/null use calculated_class_of - http://school.networkbecause.local/admin-portal/members/edit/130 tab: offline/record {{first_name}} that pulls the preferred_name for a user. If preferred name is NULL, pull the first_name of the user. {{last_name}} default_profiles * New system email to capture opt ins (for those unspecified) - turned off as default" ]

GITHUB REPOSITORY CODE: feature/task-830-system-emails-overhaul

CHANGES

	NEW FILES: 

		system_emails_lang.php
		edit_frequency_field.php

IN FILES: 

	email_center.css
	
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
	


	templates.php
	
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
				
				
				

	\fundraising.php

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
				
				


	Email.php
	
		Lots of Changes - original file is preserved for a while

	
	
	user_fill_out_profile_notification_lib.php
	
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
			
			
	
	
	events.php
	
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
		
	
	
	pyroforms_m.php

		
		CHANGE CODE I.
			
			Inside function send_email()

				FROM: $body    = $this->parser->parse_string($body, $form_data, true);
				TO: $body    = $this->parser->parse_string($body, $form_data, $return = true, $is_include = false, $streams_parse = array(), $noEmptyReplacement = true);



	events.php

		CHANGE CODE I.
			
			Inside function process_queue()

				FROM: $message = $this->CI->parser->parse_string($html, $data, true);
				TO: $message = $this->CI->parser->parse_string($html, $data, $return = true, $is_include = false, $streams_parse = array(), $noEmptyReplacement = true);




	newsletters.php

		CHANGE CODE I.
			
			Inside function sendtest($id = false)
			Inside function spamtest($id = false)

				FROM: $html = $this->parser->parse_string($html, $data, true);
				TO: $html = $this->parser->parse_string($html, $data, $return = true, $is_include = false, $streams_parse = array(), $noEmptyReplacement = true);




	events.php

		CHANGE CODE I.

			Inside function process_queue()

				FROM: $$message = $this->parser->parse_string($html, $data, true);
				TO: $$message = $this->parser->parse_string($html, $data, $return = true, $is_include = false, $streams_parse = array(), $noEmptyReplacement = true);


	
	email_center.php

		CHANGE CODE I.
			
			Inside function sendtest($id = false)
			Inside function debugtest($id = false)

				FROM: $html = $this->parser->parse_string($html, $data, true);
				TO: $html = $this->parser->parse_string($html, $data, $return = true, $is_include = false, $streams_parse = array(), $noEmptyReplacement = true);

	Parser.php

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




	MY_Parser.php

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



	events.php

		CHANGED CODE: 

			Inside function send_email

				FROM: $body = $this->ci->parser->parse_string($body, $data, true);

				TO: $body = $this->ci->parser->parse_string($body, $data, $return = true, $is_include = false, $streams_parse = array(), $noEmptyReplacement = true);



	email_templates_m.php
	
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



	
	edit.php
	
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
			
			
	
	
	email_templates.php
	
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
				
				
	
	details.php
	
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
					  .................
				   }
				} //END function _insertEmailTemplatesTable
			   

			   
				private function _addColumnsEmailTemplatesTable() 
				{
					$table = $this->db->dbprefix('email_templates');
					
					if ( ! $this->db->field_exists('frequency', $table)) {
						.................
					}
					
					....................
				} //END function _addColumnsEmailTemplatesTable



				
				private function _updateEmailTemplatesTable($updateFields = array()) 
				{
					$table = $this->db->dbprefix('email_templates');

					#update frequency_enabled field
					if ( in_array('frequency_enabled', $updateFields) ) {
						...............
					}
					
					#update frequency field
					if ( in_array('frequency', $updateFields) ) {
						..............		
					}
					
					#update weekly_newsletter body 
					if ( in_array('club_newsletter_body', $updateFields) ) {
						................
					}#END update weekly_newsletter body 
					
					..................
					
				} //END function _updateEmailTemplatesTable
