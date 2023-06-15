# Purge Cache WordPress - Tested on https://axis.lawyer/
Purge Cache WordPress PHP - No Plugin

Purge Cache Plugin
This plugin adds a button to the WordPress dashboard that allows you to clear the object cache with one click. This can be useful for debugging or testing purposes.

Installation
Use the Code Snippets WordPress Plugin to add this PHP code and activate it on your site. 

Usage
Once the plugin is activated, you will see a ‘Purge Cache’ button on the top right corner of your WordPress dashboard.
Click on the button to clear the object cache. You will see a confirmation message before the cache is purged.
If the cache is purged successfully, you will see an alert message. If an error occurs, you will see an error message.

Code:
Purge Cache WordPress

/*
Name: Purge Cache
Function: Adds Purge Cahce button to the WordPress dashboard 
*/

add_action( 'admin_bar_menu', 'add_purge_cache_button', 999 );

function add_purge_cache_button( $wp_admin_bar ) {
    if ( ! current_user_can( 'manage_options' ) ) {
        return;
    }

    $args = array(
        'id'    => 'purge-cache',
        'title' => 'Purge Cache',
        'href'  => '#',
        'meta'  => array( 'class' => 'purge-cache' )
    );
    $wp_admin_bar->add_node( $args );
}

add_action( 'admin_footer', 'add_purge_cache_script' );

function add_purge_cache_script() {
    if ( ! current_user_can( 'manage_options' ) ) {
        return;
    }
    ?>
    <script>
    jQuery(document).ready(function($) {
        $('#wp-admin-bar-purge-cache').click(function() {
            if (confirm('Are you sure you want to purge the cache?')) {
                $.ajax({
                    url: '<?php echo admin_url( 'admin-ajax.php' ); ?>',
                    data: {
                        action: 'purge_cache',
                    },
                    success: function() {
                        alert('Cache purged successfully!');
                    },
                    error: function() {
                        alert('An error occurred while purging the cache.');
                    }
                });
            }
        });
    });
    </script>
    <?php
}

add_action( 'wp_ajax_purge_cache', 'purge_cache_callback' );

function purge_cache_callback() {
    global $wp_object_cache;
    if ( ! current_user_can( 'manage_options' ) ) {
        wp_die();
    }

    wp_cache_flush();

    wp_die();
}


Code Explanation

The plugin code consists of four parts:

The plugin header that defines the plugin name and description.
The function that adds the ‘Purge Cache’ button to the admin bar menu using the admin_bar_menu hook.
The function that adds the JavaScript code to the admin footer using the admin_footer hook. The JavaScript code sends an AJAX request to the admin-ajax.php file when the button is clicked and displays an alert message based on the response.
The function that handles the AJAX request using the wp_ajax_purge_cache hook. The function checks if the current user has the manage_options capability and then calls the wp_cache_flush() function to clear the object cache. The function then terminates using the wp_die() function.
