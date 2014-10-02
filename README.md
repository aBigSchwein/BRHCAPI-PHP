Balance Rewards HC API PHP
=========

The Balance® Rewards API enables pre-qualified partners to connect into Walgreens to share 
individual member health and wellness information to be eligible to receive Balance Rewards points 
for healthy activity like walking, running, and weight management.
The API is designed to enable partners to seamlessly share individual health and lifestyle activity (Data 
Sharing), and create or validate user memberships. In order for individuals to receive points for health 
and wellness activity, they must be a Balance Rewards member and have successfully completed a 
Walgreens OAuth login or registration.
In order for Balance Rewards members to receive points for data recorded using pre-qualified partners 
applications, devices, or platform, partners must share full activity information with Walgreens. In no 
way is Walgreens able to reward activity without supporting aggregate or transaction level information.

> The flow of an integration:
  * Setup your Global Environment File
  * Gain access to the Balance Rewards API via oAuth code/callback
  * Get/Save oAuth Token from auth code
  * Post qualifying activity data to Walgreens
  * Unsubscribe users oAuth Token from posting data to Walgreens

###Setup your Global Environment File
Build a globals.php file that stores all of the important string/arrays

    $affiliateId = *"YOUR AFFILIATE ID"*;
    $apiKey = *"YOUR API KEY"*;
    $apiEndPoint = "https://m-qa2.walgreens.com/oauth/authorize.jsp?";
    $redirectURI = *"YOUR REDIRECT URI"*;
    $transID = substr((time().rand(0,time())), 0,16);
    $state = substr((time().rand(0,time())), 0,16);
    $cred_data = array(
        "endpoint"=>$apiEndPoint,
        "client_id"=>$affiliateId,
        "response_type"=>"code",
        "scope"=> "steps",
        "channel"=>"5",
        "redirect_uri"=>$redirectURI,
        "transaction_id"=>$transID,
        "state"=>$state
    );
    $serviceEndpoint = "https://services-qa.walgreens.com/api/oauthtoken/v1?";
    $headers = array('Content-Type: application/x-www-form-urlencoded');
    $request_data = array(
      "grant_type" => "authorization_code",
      "client_id" => $affiliateId,
      "client_secret" => $apiKey,
      "code" => "",
      "redirect_uri" => $redirectURI,
      "transaction_id" => $transID,
      "channel" => "5",
      "act" => "getOAuthToken",
      "state" => $state
    );
   
###Gain access to the Balance Rewards API via oAuth code/callback
Build a php function that uses the array of *$cred_data* from your *globals.php* file. Once the user clicks the link they will login with their Walgreens account and be sent back to your specified *$redirectURI*

    function createLink($cred_data)
    {
    	$queryURL = $cred_data['endpoint'].
    			"client_id=".$cred_data['client_id'].
    			"&response_type=".$cred_data['response_type'].
    			"&scope=".$cred_data['scope'].
    			"&channel=".$cred_data['channel'].
    			"&redirect_uri=".$cred_data['redirect_uri'].
    			"&transaction_id=".$cred_data['transaction_id'].
    			"&state=".$cred_data['state'];	
    	
    	echo '<a href="'.$queryURL.'">Connect to Walgreens</a>';
    }
    
###Get/Save oAuth Token from auth code
Build a php page at the location of your *$redirectURI* that contains something like this:
    
    include("./functions.php");
    include("./globals.php");
    
    $scope = $_REQUEST['scope'];
    $state = $_REQUEST['state'];
    $code = $_REQUEST['code'];
    $transID = $_REQUEST['transaction_id'];
    
    getToken($serviceEndpoint,$headers,$request_data,$code,$state,$transID);

###Post qualifying activity data to Walgreens
You can post all kinds of data, please read the (documentation)[https://github.com/WalgreensAPI/BRHCAPI-PHP] on this as it can be very extensive.
	TBA

###Unsubscribe users oAuth Token from posting data to Walgreens
Delete the token from your users database and build a php like this:
	TBA
