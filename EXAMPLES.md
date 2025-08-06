# Dropin Examples

This file contains a growing list of practical, copy-paste examples of the Dropin Protocol in action.

## Example 1: Setting a Persona

This dropin instantly tells the AI to adopt a specific character for a creative task.
AI_Source=gem2.5/User=Captain_Jack/Objective=Write_a_sea_shanty_about_AI.Persona:Character=Salty_pirate_captain/Speech_Style=Uses_pirate_slang(e.g.,'Ahoy','matey','shiver_me_timbers')/Tone=Boisterous_and_slightly_mad
code
Code
## Example 2: Providing Project Context

This dropin brings the AI up to speed on an ongoing project, ensuring its response is relevant and targeted.
AI_Source=Claude4/User=Project_Lead/Project_ID=Project_Phoenix.Objective:Draft_an_email_to_the_stakeholders.Background:We_missed_the_Q3_deadline_due_to_unforeseen_supply_chain_issues.Key_Message:We_have_a_new_plan_and_a_revised_Q4_launch_date/Emphasize_confidence_and_proactive_problem-solving.Audience:Non-technical_stakeholders/Avoid_jargon
code
Code
## Example 3: Technical Code Generation

This dropin sets up a specific programming request, including language, libraries, and coding style.
AI_Source=GPT4o/User=Dev/Task=Code_Generation.Objective:Write_a_Python_script_to_parse_a_CSV_file_and_extract_email_addresses.Requirements:Language=Python_3.9+/Libraries=pandas,re(for_regex)/Input_File=contacts.csv/Output=Print_a_list_of_unique_email_addresses.Coding_Style:Adhere_to_PEP8_standards/Include_docstrings_for_all_functions.
code
Code
## Example 4: Summarization with Constraints

This dropin provides a specific article to summarize and constrains the output format.
AI_Source=gem2.5/User=Researcher/Task=Summarization.Source_URL=https://(insert_article_url_here).Output_Format:Three_bullet_points/Total_length_under_100_words.Focus:Extract_only_the_main_financial_conclusions_of_the_article.
code
Code

