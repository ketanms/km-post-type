// Our custom post type function
function create_posttype() {

	register_post_type( 'team',
	// CPT Options
		array(
			'labels' => array(
				'name' => __( 'team' ),
				'singular_name' => __( 'team' )
			),
			'public' => true,
			'has_archive' => true,
			'rewrite' => array('slug' => 'team'),
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
        __( 'Team Meta BOx', 'myplugin_textdomain' ),
        'team_price_box_content',
        'team',
        'side',
        'high'
    );
	
}

function team_price_box_content( $post ) {
  wp_nonce_field( plugin_basename( __FILE__ ), 'team_price_box_content_nonce' );
  echo '<label for="team user"><b>Team User :</b> </label>';
  echo '<select><br/>';
  echo '<option value="Select User">Select User';
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

