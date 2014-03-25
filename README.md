### Tutorial membuat form login dengan PHP framework codeigniter ###


Pada tutorial sebelumnya, saya sudah memperkenalkan sedikit tentang MVC pada PHP framework codeigniter. nah, sekarang saya mau coba memperkenalkan sedikit tentang cara membuat Form login dengan menggunakan PHP framework codeigniter. mungkin teman-teman sudah banyak yang tahu tentang bagaimana cara membuat form login dengan PHP framework codeigniter, tapi untuk teman-teman yang belum tahu atau bahkan masih bingung bagai mana cara membuat form login dengan PHP framework codeigniter silahkan di lihat coding nya di bawah.

1. Langkah awal buatlah database `myweb_db`.

```sql

CREATE DATABASE myweb_db;

```
 
2. Buat table user pada database `myweb_db`.

```sql

CREATE TABLE `m_user` (
  `usr_id` int(11) NOT NULL auto_increment,
  `usr_nama` varchar(75) NOT NULL default 'anymous',
  `usr_name` varchar(20) NOT NULL default 'anymous',
  `usr_pwd` varchar(32) NOT NULL default '',
  `usr_isactive` tinyint(1) NOT NULL default '1',
  PRIMARY KEY  (`usr_id`)
) ENGINE=MyISAM AUTO_INCREMENT=1 DEFAULT CHARSET=latin1;

```

3. Insert data pada table user.

```sql

INSERT INTO m_user VALUES('','Administrator','Admin','Admin','1');

```
 
4. Ubah `database.php` pada `application/config`.

```php

$active_group = 'default';
$active_record = TRUE;

$db['default']['hostname'] = 'localhost';
//username mysql anda
$db['default']['username'] = 'root';
//password mysql anda
$db['default']['password'] = '';
//database anda
$db['default']['database'] = 'myweb_db';
$db['default']['dbdriver'] = 'mysql';
$db['default']['dbprefix'] = '';
$db['default']['pconnect'] = TRUE;
$db['default']['db_debug'] = TRUE;
$db['default']['cache_on'] = FALSE;
$db['default']['cachedir'] = '';
$db['default']['char_set'] = 'utf8';
$db['default']['dbcollat'] = 'utf8_general_ci';
$db['default']['swap_pre'] = '';
$db['default']['autoinit'] = TRUE;
$db['default']['stricton'] = FALSE;

```

5. Setting `autoload.php` pada `application/config` menjadi.

```php

/*
| -------------------------------------------------------------------
|  Auto-load Libraries
| -------------------------------------------------------------------
| These are the classes located in the system/libraries folder
| or in your application/libraries folder.
|
| Prototype:
|
| $autoload['libraries'] = array('database', 'session', 'xmlrpc');
*/

$autoload['libraries'] = array('database','session');


/*
| -------------------------------------------------------------------
|  Auto-load Helper Files
| -------------------------------------------------------------------
| Prototype:
|
| $autoload['helper'] = array('url', 'file');
*/

$autoload['helper'] = array('url','form');

```

6. Buat controller login dengan nama `login.php` pada `application/controllers`, yang berisikan:

```php

<?php
 
 class login extends CI_Controller
 {
 
  function __construct()
  {
   //membuat construktor;
   parent::__construct();
   //meload helper form dan url
   $this->load->helper(array('form','url'));
   //meload model login
   $this->load->model('m_login');
  }
  
  function index()
  {
   //membuat variable judul web yang ditampilkan sebagai judul browser
   $data['title'] = 'MYWEB';
   //membuat nama header yang ditampilkan pd form login
   $data['loginheader'] = 'Login';
   //membuat string gagal dimana nantinya digunakan untuk validasi
   $data['gagal'] = '';
   //meload view login
   $this->load->view('v_login',$data);
  }
  
  function cekuser()
  {
   //memanggil model login
   $data['query'] = $this->m_login->cekdb();
   //mengecek isi dari model login
   if($data['query']==null)
   {
    return false;
   }
   else
   {
    return $data['query'];
   }
  }
  
  function proseslogin()
  {
   //membuat variable judul web yang ditampilkan sebagai judul browser
   $data['title'] = 'MYWEB';
   //membuat nama header yang ditampilkan pd form login
   $data['loginheader'] = 'Login';
   //megecek isi dari fungsi cekuser diatas
   //jika data ada maka login berhasil
   if($this->cekuser()==true)
   {
    //membuat variable logo yang nanti ditampilkan pd view
    $data['logo'] = 'MYWEB';
    //membuat variable account yang nanti ditampilkan pd view
    $data['username'] = 'Account: '.$this->input->post('username');
    //menyimpan data pada array
    $newdata = array
    (
     'username'=>$data['username'],
     'islogin'=>true
    );
    //membuat sesion
    $this->session->set_userdata($newdata);
    //menampilkan view home
    $this->load->view('v_home',$data);
   }
   else
   {
    //membuat validasi gagal yang ditampilkan pada form login
    $data['gagal'] = '<div id="notification">Maaf, Proses Login Gagal!<BR>1. Username atau Password anda salah.<BR>2. Coba periksa kembali keadaan <span class="red">Caps lock</span>.</div>';
    //memanggil view login
    $this->load->view('v_login',$data);
   }
  }
  
  function logout()
  {
   //menghapus sesion
   $this->session->sess_destroy();
   //kembali ke form login
   redirect('login/index');  
  }
 
 }
?>

```

7. Selanjutnya membuat model login, dimana pada model ini dikhususkan untuk koneksi pada database. simpan dengan nama `m_login.php` pada `application/models`.

```php

<?php
 class m_login extends CI_Model
 {
  public function cekdb()
  {
   //mengambil data dari form login username
   $username = $this->input->post('username');
   //mengambil data dari form login password
   $password = $this->input->post('password');
   
   //sql select usr_id
   $this->db->select('usr_id');
   //sql where usr_name = username yang diambil dari form login
   $this->db->where('usr_name', $username);
   //sql where usr_pwd = password yang diambil dari form login
   $this->db->where('usr_pwd', md5($password));
   //sql where usr_isactive = aktif
   $this->db->where('usr_isactive','1');
   //sql tabel yang dipilih m_user
   $query = $this->db->get('m_user');
   //perulangan nilai array yang berisi data dari query sql diatas
   foreach($query->result() as $row)
   {
    return $row->usr_id;
   }
  }
   
 }

?>

```

8. Untuk penjelasan tentang perulangan nilai array, akan saya jelaskan pada tutorial "Fungsi Foreach" selanjutnya  
Membuat view login, dimana view ini adalah merupakan hasil yang akan dilihat oleh user. disimpan dengan nama `v_login.php` pada `application/views`.

```html

<html>
 <head>
  <!-- menampilkan judul pada browser -->
  <title><?=$title?></title>
  <meta http-equiv="Content-Type" content="text/html; charset=iso-8859-1">
  <link rel="stylesheet" type="text/css" media="all" href="<?php echo base_url();?>/login.css" />  
 </head>
 <body>
  <?php echo form_open("login/proseslogin"); ?>
  <div id="wrap">
   <div id="maincontent">
    <!-- menampilkan header login -->
    <div id="header"><?=$loginheader?></div>
    <div id="content">
      <!-- menampilkan validasi login -->
      <?=$gagal?>
      <div id="label">Usernama :</div>
      <div id="form"><input type="text" name="username"></div>
      <div id="label">Password :</div>
      <div id="form"><input type="password" name="password"></div>
      <div id="label">&nbsp;</div>
      <div id="form"><input type="submit" value="Login"/></div>
    </div>
    <div id="footer"></div>
   </div>
  </div>
  </form>
 </body>
</html>

```

9. Buat view `home.php` pada `application/views`. Dimana nanti jika proses login sukses, maka akan menampilkan `home.php`.

```html

<html>
 <head>
  <!-- menampilkan judul pada browser -->
  <title><?=$title?></title>
  <link rel="stylesheet" type="text/css" media="all" href="<?php echo base_url();?>/css/home.css" />  
 </head>
 <body  topmargin="0" leftmargin="0">
  <div id="wrap">
   <div id="maincontent">
    <div id="header">
     <!-- menampilkan judul logo di header -->
     <div class="logo"><?=$logo?></div>
     <!-- menampilkan account name di header -->
     <div class="account"><?=$username?>, <a href="<?php echo site_url(); ?>/login/logout">Logout</a></div>
     <div id="clear"></div>
    </div>
   </div>
  </div>
 </body>
</html>

```

10. Membuat css login, untuk memperindah tampilan form, disimpan dengan nama `login.css` pada root folder CodeIgniter `xampp/htdocs/CodeIgniter/`.

```css

/* 
Theme Name: Away Login Theme 
Description: Theme #1
Author: Darmawan
Author URI: http://darmawantm.blogspot.com 
Version: 1.0 
*/  
body {  
 font-family: tahoma, "Bitstream Charter", serif;  
 font-size:12px;
 overflow:scroll;
} 
#wrap {  
 border:solid 2px #999999; 
 width:270px;  
 margin:0 auto;  
 margin-top:200px;
 padding:2px;  
 background:#9999CC;  
}  
#maincontent {  
 border:solid 2px #fff; 
 background:#9999CC; 
 padding: 5px;
}  
#header{
 padding:5px;
 font-weight:bold;
 font-size:14px;
}
#content{
 border:solid 2px #fff; 
 background:#fff; 
 padding: 5px;

}
#notification{
 background:#FFFF99;
 border:solid 1px #FFCC00; 
 padding:3px;
}
#label{
 float:left;
 width:70px;
  margin:5px;
}
#form{
 margin:5px;
}
.red{
 color:#CC0000;
}

```

11. Setting `routes.php` pada `application/config`, menjadi.

```php

| -------------------------------------------------------------------------
| RESERVED ROUTES
| -------------------------------------------------------------------------
|
| There area two reserved routes:
|
| $route['default_controller'] = 'welcome';
|
| This route indicates which controller class should be loaded if the
| URI contains no data. In the above example, the "welcome" class
| would be loaded.
|
| $route['404_override'] = 'errors/page_missing';
|
| This route will tell the Router what URI segments to use if those provided
| in the URL cannot be matched to a valid route.
|
*/

$route['default_controller'] = "login";
$route['404_override'] = "";

$route['scaffolding_trigger'] = "post";

``` 

12. Jalankan dengan membuka `http://localhost/myweb`
Selesai. Silahkan tunggu turotial selanjutnya.
