// Our custom post type function
function create_posttype() {

	register_post_type( 'Team',
	// CPT Options
		array(
			'labels' => array(
				'name' => __( 'Team' ),
				'singular_name' => __( 'Team' )
			),
			'public' => true,
			'has_archive' => true,
			'rewrite' => array('slug' => 'Team'),
			"supports" => array( "title", "editor", "thumbnail", "page-attributes", "post-formats" ),
			'register_meta_box_cb' => 'team_price_box'
		)
	);
}
// Hooking up our function to theme setup
add_action( 'init', 'create_posttype' );


add_action( 'add_meta_boxes', 'team_price_box' );
function team_price_box() {
    add_meta_box( 
        'team_price_box',
        __( 'Team Meta Box', 'myplugin_textdomain' ),
        'team_price_box_content',
        'team',
        'side',
        'high'
    );
	
}

function team_price_box_content( $post ) {
  wp_nonce_field( plugin_basename( __FILE__ ), 'team_price_box_content_nonce' );
  $users = get_users();
  echo '<label for="team user"><b>Team User :</b> </label>';
  echo '<select><br/>';
  echo '<option>Select User';
  foreach($users as $user) {
  	echo '<option value='.$user->data->ID.'>'.$user->data->user_login.'</option>';
  }
  echo '</select><br/><br/>';

  echo '<label for="area_size"><b>Area Size :</b> </label>';
  echo '<input type="text" id="area_size" name="area_size" placeholder="Enter Area Size" /><br/><br/>';
  echo '<label for="area_interest"><b>Area Interest :</b></label><br/>';
  echo '<input name="development" type="checkbox" value="development">development<br/>';
  echo '<input name="Desinging" type="checkbox" value="Desinging">Desinging<br/>';
  echo '<input name="Tesing" type="checkbox" value="Tesing">Tesing<br/>';
  echo '<input name="Any" type="checkbox" value="Any">Any<br/>';
  
}


add_action( 'save_post', 'team_price_box_save' );
function team_price_box_save( $post_id ) {

  if ( defined( 'DOING_AUTOSAVE' ) && DOING_AUTOSAVE ) 
  return;

  if ( !wp_verify_nonce( $_POST['team_price_box_content_nonce'], plugin_basename( __FILE__ ) ) )
  return;

  if ( 'page' == $_POST['post_type'] ) {
    if ( !current_user_can( 'edit_page', $post_id ) )
    return;
  } else {
    if ( !current_user_can( 'edit_post', $post_id ) )
    return;
  }
  $team_price = $_POST['team_price'];
  update_post_meta( $post_id, 'team_price', $team_price );
   $team_user = $_POST['team_user'];
  update_post_meta( $post_id, 'team_user', $team_user );
}


function custom_metaboxes1() {
	    add_meta_box('gallery_shortcode_info', 'Shortcode info', 'gallery_shortcode_fn1', 'team', 'side');
}
add_action('add_meta_boxes','custom_metaboxes1');

function gallery_shortcode_fn1()
{
	global $post;
	
?>
	<input type="text" name="shrtcode" value="[zoomGallery1 id='<?php echo $post->ID; ?>']" readonly="readonly"/>
    
<?php 
		
}



    add_shortcode( 'zoomGallery1', 'display_custom_post_type' );

    function display_custom_post_type(){
        $args = array(
            'post_type' => 'team',
			'posts_per_page' =>1,
            'post_status' => 'publish'
			
        );

        
        $query = new WP_Query( $args );
        if( $query->have_posts() ){
           
            while( $query->have_posts() ){
                $query->the_post();
                 echo '<span><b>Title:</b></span>'; 
				 echo the_title(); 
				 echo '</br/>';
				 echo '<span><b>Description:</b></span>'; 
				 echo the_content();
				 echo $user->data->user_login;
				
            }
           
        }
        wp_reset_postdata();
      }

