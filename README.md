WordPress Settings Framework
============================

The WordPress Settings Framework aims to take the pain out of creating settings pages for your WordPress plugins
by effectively creating a wrapper around the WordPress settings API and making it super simple to create and maintain
settings pages.

This repo is actually a working plugin which demonstrates how to implement WPSF in your plugins. See `wpsf-test.php`
for details.

Setting Up Your Plugin
----------------------

1. Drop `wp-settings-framework.php` in the root of your plugin folder.
2. Create a "settings" folder in your plugin root.
3. Create a settings file in your new "settings" folder (e.g. `settings-general.php`)

Now you can set up your plugin like:

    class WPSFTest {
    
        private $plugin_path;
        private $wpsf;
    
        function __construct() 
        {	
            $this->plugin_path = plugin_dir_path( __FILE__ );
            
            // Include and create a new WordPressSettingsFramework
            require_once( $this->plugin_path .'wp-settings-framework.php' );
            $this->wpsf = new WordPressSettingsFramework( $this->plugin_path .'settings/settings-general.php' );
            // Add an optional settings validation filter (recommended)
            add_filter( $this->wpsf->get_option_group() .'_settings_validate', array(&$this, 'validate_settings') );
            
            // ...
        }
        
        // This page is added using add_menu_page()
        function settings_page()
    	{
    	    ?>
    		<div class="wrap">
    			<div id="icon-options-general" class="icon32"></div>
    			<h2>WP Settings Framework Example</h2>
    			<?php 
    			// Output your settings form
    			$this->wpsf->settings(); 
    			?>
    		</div>
    		<?php
    	}
    	
    	function validate_settings( $input )
    	{
    	    // Do your settings validation here
    	    // Same as $sanitize_callback from http://codex.wordpress.org/Function_Reference/register_setting
        	return $input;
    	}
        
        // ...
        
    }
    
Your settings values can be accessed by getting the whole array:

    // Get settings
	$settings = wpsf_get_settings( $this->plugin_path .'settings/settings-general.php' );
		
Or by getting individual settings:

	// Get individual setting
	$setting = wpsf_get_setting( wpsf_get_option_group( $this->plugin_path .'settings/settings-general.php' ), 'general', 'text' );
	

The Settings Files
------------------

The settings files work by filling the global `$wpsf_settings` array with data in the following format:

    $wpsf_settings[] = array(
        'section_id' => 'general', // The section ID (required)
        'section_title' => 'General Settings', // The section title (required)
        'section_description' => 'Some intro description about this section.', // The section description (optional)
        'section_order' => 5, // The order of the section (required)
        'fields' => array(
            array(
                'id' => 'text',
                'title' => 'Text',
                'desc' => 'This is a description.',
                'type' => 'text',
                'std' => 'This is std'
            ),
            array(
                'id' => 'select',
                'title' => 'Select',
                'desc' => 'This is a description.',
                'type' => 'select',
                'std' => 'green',
                'choices' => array(
                    'red' => 'Red',
                    'green' => 'Green',
                    'blue' => 'Blue'
                )
            ),
            
            // add as many fields as you need...
            
        )
    );
    
Valid `fields` values are:

* `id` - Field ID
* `title` - Field title
* `desc` - Field description
* `type` - Field type (text/textarea/select/radio/checkbox/checkboxes)
* `std` - Default value (or selected option)
* `choices` - Array of options (for select/radio/checkboxes)

See `settings/settings-general.php` for an example of possible values.


API Details
-----------

    new WordPressSettingsFramework( string $settings_file [, string $option_group = ''] )
    
Creates a new settings [option_group](http://codex.wordpress.org/Function_Reference/register_setting) based on a setttings file.

* `$settings_file` - path to the settings file
* `$option_group` - optional "option_group" override (be default this will be set to the basename of the settings file)

<pre>wpsf_get_option_group( $settings_file )</pre>
    
Converts the settings file name to option group id

* `$settings_file` - path to the settings file

<pre>wpsf_get_settings( string $settings_file [, string $option_group = ''] )</pre>
    
Get an array of settings by the settings file or option_group

* `$settings_file` - path to the settings file
* `$option_group` - optional "option_group" override

<pre>wpsf_get_setting( $option_group, $section_id, $field_id )</pre>

Get a setting from an option group

* `$option_group` - option group id
* `$section_id` - section id
* `$field_id` - field id

Note: You can use `wpsf_get_option_group()` to get the option group id from the settings file path.

Credits
-------

The WordPress Settings Framework was created by [Gilbert Pellegrom](http://gilbert.pellegrom.me) from [Dev7studios](http://dev7studios.com)

The WordPress Settings Framework is released under the **MIT license** and is free to use and abuse.

Please contribute by [reporting bugs](WordPress-Settings-Framework/issues) and submitting [pull requests](WordPress-Settings-Framework/pulls).
