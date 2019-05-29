# last-seen
Last seen

Controller:

	function users_list() {
	$q = 'SELECT * FROM users inner join online_users on users.users_id = online_users.uid where users.users_id != 		"'.sales_person_id().'" ';
	$all_users['all_users'] = $this->db->query($q)->result_array();

	}

function last_seen() {
		$uid = $this->input->post('uid');
		$date = date('d-m-Y H:i:s'); 
		$currentDate = strtotime($date); 
		$futureDate = $currentDate+(60*1);
		// $formatDate = date("Y-m-d H:i:s", $futureDate);

		$curr_uid = $this->session->userdata("login_session_data")["user_id"];

		$update_time = 'UPDATE online_users SET last_seen = "'.$futureDate.'" WHERE  uid = "'.$uid.'" ';
		$updated = $this->db->query($update_time);
		$q = 'SELECT * FROM online_users WHERE last_seen > "'.$currentDate.'" AND ou_id != "'.$curr_uid.'" ';
		$online_users = $this->db->query($q)->result_array();
		// echo  $this->db->last_query();
		echo json_encode($online_users);

	}
  
  
  // view
  <li class="nav-item online_users" data-toggle="tooltip" data-placement="right" title="Online Users">
      <a class="nav-link nav-link-collapse collapsed online_userss manage_salesteam" data-toggle="collapse" href="#online_users" data-parent="#exampleAccordion">
        <i class="fa fa-fw fa-envelope"></i>
        <span class="nav-link-text">Online (<i class="users_count">0</i>) </span>
      </a>
      <ul class="sidenav-second-level collapse" id="online_users">

        <div class="online_usersssss"></div>

      </ul>
    </li>

    <li>
      <ul>
      <?php
        foreach ($all_users as $key => $value) {
          if (get_timeago( $value['last_seen'] ) == 'Now') {
           $cicle = '<i class=" fa fa-circle fa-spin" style="color:#27d62a;"></i>';
          } else {
            $cicle = '';
          }
        ?> <li><?=$value['first_name'].' '.$value['last_name']?><br> <span style="font-size: x-small;"> <?=$cicle?> Active: <?=get_timeago( $value['last_seen'] )?> </span></li> <?php 
      } ?>
      </ul>
    </li>
    
    
    // App_helper
    function get_timeago( $ptime ) {
		$estimate_time = time() - $ptime;

		if( $estimate_time < 1 )
		{
			return 'Now';
		}

		$condition = array( 
			12 * 30 * 24 * 60 * 60  =>  'year',
			30 * 24 * 60 * 60       =>  'month',
			24 * 60 * 60            =>  'day',
			60 * 60                 =>  'hour',
			60                      =>  'minute',
			1                       =>  'second'
		);

		foreach( $condition as $secs => $str )
		{
			$d = $estimate_time / $secs;

			if( $d >= 1 )
			{
				$r = round( $d );
				return ' ' . $r . ' ' . $str . ( $r > 1 ? 's' : '' ) . ' ago';
			}
		}
	}
  
  // Footer
  <script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/jquery/2.2.0/jquery.min.js"></script>
<script type="text/javascript">
	$(document).ready(function(){
		last_seen();
		function last_seen() {
			var uid = '<?=$this->session->userdata("login_session_data")["user_id"]?>';
			$.ajax({
				url  : '<?=base_url()?>dashboard/last_seen',
				type : 'post',
				dataType : 'json',
				data : {uid:uid},
				success: function (data) {
					$('.online_usersssss').empty();
					$('.users_count').text(data.length);
					for (var x = 0; x < data.length; x++) {
						$('.online_usersssss').append('<a href="#"><li> <i class=" fa fa-circle fa-spin" style="color:#27d62a;"></i> '+data[x].uid+'</li></a>');
					}
				}
			});
		}

		setInterval(function(){ 
			last_seen(); 
		}, 9000);

	});
</script>
  
