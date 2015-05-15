<apex:page docType="html-5.0"
           showHeader="false" sidebar="false"
           standardController="Order__c" extensions="IceCreamAppExtentionController">          
    <apex:stylesheet value="{!URLFOR($Resource.MobileSample_Resources_jQueryMobile, 'jquery.mobile-1.3.0.min.css')}"/>
    <apex:includeScript value="{!URLFOR($Resource.MobileSample_Resources_jQueryMobile, 'jquery-1.9.1.min.js')}"/>
    <apex:includeScript value="{!URLFOR($Resource.MobileSample_Resources_jQueryMobile, 'jquery.mobile-1.3.0.min.js')}"/>
    <apex:includeScript value="{!URLFOR($Resource.MobileSample_Resources_jQueryMobile, 'cordova.force.js')}"/>
    <apex:includeScript value="{!URLFOR($Resource.MobileSample_Resources_jQueryMobile, 'backbone/underscore-1.4.4.min.js')}"/>
    <apex:includeScript value="{!URLFOR($Resource.MobileSample_Resources_jQueryMobile, 'force.entity.js')}"/>
    <apex:includeScript value="{!URLFOR($Resource.MobileSample_Resources_jQueryMobile, 'SObjectData.js')}"/>
    <apex:includeScript value="{!$Resource.StarWebTrader}"/>
    <apex:includeScript value="{!$Resource.StarWebPrintBuilder}"/>
 
    <style>
        .ui-mobile .ui-page-active {
            display: block;
            overflow: visible;
            background: white;
        }
        .ui-btn-up-b:visited, .ui-btn-up-b a.ui-link-inherit{
            color: #7BC043;
            
        }
        .ui-checkbox-on .ui-icon, .ui-radio-on .ui-icon {
            background-color: #7BC043;
        }
        .ui-li, .ui-li.ui-field-contain {
            display: block;
            margin: 0;
            position: relative;
            overflow: visible;
            text-align: left;
            border-width: 0;
            border-top-width: 0px;
            border-bottom-width: 0px;
            text-align: center;
        }
        .ui-li.ui-last-child, .ui-li.ui-field-contain.ui-last-child {
            border-bottom-width: 0px;
            text-align: center;
        }
        .msg-class{
            color:red;
            font-size: 16px;
            font-weight: bold;
            text-align: center;
            
        }
        .canvas{
            //width: 150px;
            //height: 150px;
            //display:none;
            //visibility:hidden;
        }

    </style>
    
    <head>
        
        <title>Orders</title>
        <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
        <c:remotetk />
        
       
        <script type="text/javascript">

        //******************************************************************** TSHIRT APPLICATION FOR XACTLY COMP CLOUD ****************************************************************************
        //****************************** USER SELECTS A SHIRT SIZE, COLOR SHIRT, AND SLOGAN *****************************  
        
       
           
            var $j = jQuery.noConflict(); 
            var client = new remotetk.Client();
            Force.init(null,null,client,null);
            
            var Orders = new SObjectData();
            Orders.errorHandler = displayError;
            
            $j(document).ready(function() {
                regBtnClickHandlers();
                getAllOrders();
               
                
            });
            
            function getAllOrders() {
                Orders.fetch('soql','SELECT id,name,Sizes__c,Color__c,Nice_Saas__c,SasSfaction__c  from Order__c where Sizes__c !=null LIMIT 3',function() {
                    showOrders(Orders.data());
                });
            }

            function showOrders(records) {    
                $j('#cList').empty();
                $j.each(Orders.data(),
                    function() {
                    var newLi = $j('<li></li>');
                                
                    var newLink = $j('<a id="' +this.Id+ '" data-transition="flip">' + this.name + '</a>');
                    newLink.click(function(e) {
                        e.preventDefault();
                        $j.mobile.showPageLoadingMsg();
                        $j('#size').val(Orders.findRecordById([this.id]).Sizes__c);
                        $j('#colors').val(Orders.findRecordById([this.id]).Color__c);
                        $j('#OrderId').val(Orders.findRecordById([this.id]).Id);
                        $j('#error').html('');
                       fsc
                        $j.mobile.changePage('#detailpage', {changeHash: true});
                    });
                    newLi.append(newLink);            
                    newLi.appendTo('#cList');
                  //  x++;
                  });
                
                $j.mobile.hidePageLoadingMsg();
                $j('#cList').listview('refresh');
            }      
        
            function createOrder(e){
                e.preventDefault();
                //console.log('####e: ' + e);
                var cId = $j('#OrderId').val();
                
                var record = Orders.findRecordById(cId);
                if(record == null) { 
                    
                    //new record
                    record = Orders.create();
                   
                    
                }
                 
                
                record.Name= $j('#ordername').val();
                
                
                record.Sizes__c = $j('.sizeclass:checked').val();
                
              
                record.Color__c = $j('.colorclass:checked').val();
                
                
                var check1 = $j('.sloganclass1:checked').val();
                if($j('.sloganclass1:checked').val()){
                    record.Nice_SaaS__c=true;
                }
                if($j('.sloganclass2:checked').val()){
                    record.SaaSfaction__c=true;
                }
        
                parseObjectinfo(record);
                
                Orders.sync(record,successCallback);
                
                $j('#ordername').val('');
                //$j('#email').val('');
                $j('.sizeclass').attr("checked",false).checkboxradio("refresh");
                $j('.colorclass1').attr("checked",false).checkboxradio("refresh");
                $j('.colorclass2').attr("checked",false).checkboxradio("refresh");
                $j('.sloganclass1').attr("checked",false).checkboxradio("refresh");
                $j('.sloganclass2:checked').attr("checked",false).checkboxradio("refresh");            
                           
                Visualforce.remoting.Manager.invokeAction(
                    '{!$RemoteAction.IceCreamAppExtentionController.getOrderId}', 
                    
                    function(result, event){;
                        if(event.status){
                            console.log('@@@ result : '+result);
                            JSONOrderResult = JSON.stringify(result);
                            
                            //alert(JSONOrderResult);
                            var OrderResult = '<table style="color:#7BC043; background-color: white;" data-role="table" data-mode="columntoggle" class="ui-responsive ui-shadow" id="myTable"><h2 style="color:#7BC043;font-size:18px;">Voila! Your TShirt is now configured. You can pick up your shirt in 5 minutes. Thank you for experiencing the power of Apttus CPQ on the Salesforce1 platform. We hope you enjoy Xactly CompClous 2015!</h2><br/><h2 style="color:#7BC043;font-size:18px;">Order Summary</h2><br/>';
                                 var json = $j.parseJSON(JSONOrderResult);
                                		$j(json).each(function(i,val){
                                			$j.each(val,function(k,v){
                                				  console.log(k+" : "+ v);  
                                				  if(!v){
                                				      
                                				  }else{
                                				     
                                				     if(k == 'Id'){
                                				         
                                				     }else{
                                    				      if(typeof v =='boolean')
                                    				        v= 'Yes';
                                    				      if(k =='Name')
                                    				        k= 'Name';
                                    				      if(k =='Flavors__c')
                                    				        v = v.split(";").join(", ");
                                    				        k = k.split("__c").join(" ");
                                                            k = k.split("_").join("");
                                                           
                                    				        OrderResult = OrderResult + '<tr><td style="color:#7BC043;width:200px;font-size:12px;">' + k +'</td><td style="color:#7BC043;width:200px;font-size:12px;">' + v +'</td></tr>';	
                                				      }
                                		          }
                                		});
                                		});
                           
                                    OrderResult = OrderResult + '</table>';
                                    //alert(OrderResult);
                                    $j(".divOrderResult").append(OrderResult);
                           
                                    
                        } else if (event.type === 'exception'){
                        console.log(result);
                       }
                    });
                 /*   
                 Visualforce.remoting.Manager.invokeAction(
                    '{!$RemoteAction.IceCreamAppExtentionController.getAttachment}', 
                    
                    function(result, event){;
                        if(event.status){
                            console.log('@@@ result : '+result);
                            var attachmentId = result;
                            
                            //attchurl = 'https://c.na16.content.force.com/servlet/servlet.FileDownload?file=' + attachmentId;
                            attchurl = 'https://c.na16.content.force.com/servlet/servlet.ImageServer?id=' + attachmentId + '&oid=00Dj0000001tDvL';
                            gcp(attchurl);
                        } else if (event.type === 'exception'){
                        console.log(result);
                       }
                    }); */
                    
                
                 
                 $j.mobile.showPageLoadingMsg();
                
            }
        
            function parseObjectinfo(record){
              var name = record.Name;
                //alert(name);
              var sizes = record.Sizes__c;
                //alert(scoops);
              var colors = record.Colors__c;
                // alert(flavors);
              var s1 = record.Nice_Saas__c;
              var s2 = record.SaaSfaction__c;
              
               onDrawReceipt(name,sizes,colors,s1,s2);
              onSendMessage();
                
             }
        
            function deleteOrder(e){
                e.preventDefault();
                Orders.remove(Orders.findIndexById($j('#OrderId').val()),successCallback);
            }
            
            function successCallback(r){
                getAllOrders();
                $j.mobile.changePage('#ordersuccess', {changeHash: true,transition: "flip"});
            }
        
            function displayError(e){
                console.log(e);
                $j('#error').html(e[0].message);
            }
        
            function regBtnClickHandlers() {
                $j('#add').click(function(e) {
                    e.preventDefault();
                    $j.mobile.showPageLoadingMsg();
                    $j('#ordername').val('');
                    $j('#email').val('');
                    //$j('#toppingclass1').val('');
                    // $j('#toppingclass2').val('');
                    //  $j('#toppingclass3').val('');
                    //   $j('#toppingclass4').val('');
                    //    $j('#flavorclass').attr("checked",false).checkboxradio("refresh");
                     //    $j('#sizeclass').attr("checked",false).checkboxradio("refresh");
                    $j('#error').html('');
                    $j('#OrderId').val('');
                    //$j.mobile.changePage('#detailpage', {changeHash: true});
                   // $j.mobile.hidePageLoadingMsg();            
                });
        
                $j('#placeorder').click(function(e) {
                   createOrder(e);
                });
        
                $j('#delete').click(function(e) {
                   deleteOrder(e);
                });
            }
            function checkName(selection){
            
                if( $j('#ordername').val()=='' || $j('#email').val()==''){
                    
                    $j('.ordermsgclass').show();
                    
                }
                if( $j('#ordername').val()!='' && $j('#email').val()!=''){
                    //add name and email here
                    $j('.ordermsgclass').hide();
                    $j('.sizeselectmsgclass').hide();
                    $j('.sloganselectmsgclass    ').hide();
                    if(selection=='sizeselection')
                        //here to add
                        $j.mobile.changePage('#sizeselection', {changeHash: true,transition: "flip"});
                        
                    if(selection=='colorsselection'){
                        
                        if(!$j('.sizeclass:checked').val())
                              $j('.sizeselectmsgclass').show();
                        else{
                              //add here
                              $j.mobile.changePage('#colorsselection', {changeHash: true,transition: "flip"});
                        }
                    }
                    
                    if(selection=='sloganselection'){

                        if((!$j('.sauceclass1:checked').val()) && (!$j('.sauceclass2:checked').val()) && (!$j('.sauceclass3:checked').val()))
                              $j('.toppingselectmsgclass    ').show();
                        else{
                            //add here
                              $j.mobile.changePage('#toppingselection', {changeHash: true,transition: "flip"});
                    
                        }
                   }
               }   
            
            }
            var counter=0;
            function checkmax(){
            
                if($j('.colorclass1:checked').val())
                    counter++;
                if($j('.colorclass2:checked').val())
                    counter++;
                
                if(counter==0)
                     $j('.colormsgclassnone').show(); 
                else {             
                
                    if($j('.sizeclass:checked').val()=='Small'){
                                   
                         if(counter>1){
                             $j('.colormsgclassone').show();
                         }else{
                             $j('.colormsgclassone').hide();
                             $j('.colormsgclasstwo').hide();
                             $j('.colormsgclassnone').hide(); 
                             
                             $j.mobile.changePage('#sauceselection', {changeHash: true,transition: "flip"});
                         }    
                         
                    }    
                    if($j('.sizeclass:checked').val()=='Medium'){
                         console.log('counter : ' + counter ); 
                         if(counter>2){
                             $j('.colormsgclasstwo').show(); 
                             
                         }else{
                             $j('.colormsgclassone').hide();
                             $j('.colormsgclasstwo').hide();
                             $j('.colormsgclassnone').hide();
                             $j.mobile.changePage('#sauceselection', {changeHash: true,transition: "flip"});
                         } 
                    }   
                    counter=0;
                }    
            }
            var counter=0;
            function checkmax2(){
            
                if($j('.colorclass1:checked').val())
                    counter++;
                if($j('.colorclass2:checked').val())
                    counter++;              
                if(counter==0)
                     $j('.colormsgclassnone').show(); 
                else {             
                
                    if($j('.sizeclass:checked').val()=='Small'){
                                   
                         if(counter>1){
                             $j('.colormsgclassone').show();
                         }else{
                             $j('.colormsgclassone').hide();
                             $j('.colormsgclasstwo').hide();
                             $j('.colormsgclassnone').hide(); 
                             
                             $j.mobile.changePage('#toppingselection', {changeHash: true,transition: "flip"});
                         }    
                         
                    }    
                    if($j('.sizeclass:checked').val()=='Medium'){
                         console.log('counter : ' + counter ); 
                         if(counter>2){
                             $j('.colormsgclasstwo').show(); 
                             
                         }else{
                             $j('.colormsgclassone').hide();
                             $j('.colormsgclasstwo').hide();
                             $j('.colormsgclassnone').hide();
                             $j.mobile.changePage('#toppingselection', {changeHash: true,transition: "flip"});
                         } 
                    }   
                    counter=0;
                }    
            }
        
            $j(document).on('click', '.clearmsgclass', function(){
             
             //function clearmsg(){
            
                $j('#ordername').val('');
                $j('#email').val('');
                $j('.ordermsgclass').hide();
                $j('.sizeselectmsgclass').hide();
                $j('.toppingselectmsgclass').hide();
                if($j('.sizeclass:checked').val())
                    $j('.sizeclass').attr("checked",false).checkboxradio("refresh");
                if($j('.colorclass1:checked').val())
                    $j('.colorclass1').attr("checked",false).checkboxradio("refresh");
                if($j('.colorclass2:checked').val())
                    $j('.colorclass2').attr("checked",false).checkboxradio("refresh");         
                if($j('.sloganclass1:checked').val())    
                    $j('.sloganclass1').attr("checked",false).checkboxradio("refresh");
                if($j('.sloganclass2:checked').val())      
                    $j('.sloganclass2:checked').attr("checked",false).checkboxradio("refresh");
              
                
                $j.mobile.changePage('#orderpage', {changeHash: true,transition: "flip"});
                
                location.reload();
                
            });
            
            function hideallmsgs(){
                
                $j('.ordermsgclass').hide();
                $j('.sizeselectmsgclass').hide();            
                $j('.colormsgclassone').hide();
                $j('.colormsgclasstwo').hide();
                $j('.colormsgclassnone').hide();     
                
            }
            function checksize(){
                
                if(!$j('.sizeclass:checked').val())
                     $j('.sizeselectmsgclass').show();
                else
                    $j.mobile.changePage('#colorsselection', {changeHash: true,transition: "flip"});        
            }

           
            function colorimagedisplay() {
                
              
               
               if($j('.colorclass1:checked').val())
                    $j('.checkbox-color-one-image').show();
                else
                    $j('.checkbox-color-one-image').hide();
                    
                if($j('.colorclass2:checked').val())
                    $j('.checkbox-color-two-image').show();
                else
                    $j('.checkbox-color-two-image').hide();
              
                    
             
                
            }
            
        
        		function sloganimagedisplay() {
                
                if($j('.sauceclass1:checked').val())
                     $j('.checkbox-slogan-one-image').show();
                else
                     $j('.checkbox-slogan-one-image').hide();
                
                if($j('.sloganclass2:checked').val())
                    $j('.checkbox-slogan-two-image').show();
                else
                    $j('.checkbox-slogan-two-image').hide();
                                    
               }
            
        </script>    
    </head>
 <form>   
    <body>
     
        <div data-role="page" data-theme="b" id="homepage">                
            <br/>
            <div data-theme="b"  align="center">
                <img src="{!URLFOR($Resource.cover)}" alt="Smiley face" />
                <br/>
                 
                <ul style="font-size: 18px; color:#7BC043; list-style-type: none; list-style-position:inside; margin:2; padding:0; ">
                    <li><strong>Welcome to Xactly CompCloud 2015!</strong></li>
                    <li>Experience the power of Apttus CPQ on the Salesforce 1 Platform, where configuration doesn’t have to be all about work. Click order to start configuring your very own custom Tshirt!</li>
                </ul>
                <ul style="font-size: 18px; color:#7BC043; list-style-type: none; list-style-position:inside; margin:2; padding:0; ">
                
 					<li><strong>Dates and Times for Custom T-Shirts</strong></li>
                    <li>Tuesday, May 19</li>
                    <li>Ready to pick-up:</li>
                    <li>12:00 – 1:00pm (Lunch)</li>
                    <li>3:00 - 4:00pm (Break)</li>
                    <li>5:00 – 6:00pm (Reception)</li>
                    

                </ul>
                <br/>
                    <a href='javascript:void(0);' align="center" id="homepageId" class='ui-btn-center clearmsgclass' data-role="button" data-direction="reverse" data-transition="flip"  style="background:#7bc043; color:white;">Order</a>
            </div>
           
            
        </div>
        
        <div data-role="page" data-theme="b" id="ordersuccess"  align="center">
            <div>
                   <img src="{!URLFOR($Resource.Apttussf)}" alt="Smiley face" />
                <br/>
                <img src="{!URLFOR($Resource.frozen)}" alt="Smiley face" />
                <br/>                  
            </div>
             <br/>
            <div class="divOrderResult"></div>          
             <br/>
            <br/>
            <a href='#homepage' id="homepageId" class='ui-btn-center' data-role="button" data-direction="reverse" data-transition="flip" style="background:#7bc043; color:white;">Home</a>
                    
            
             
         </div> 
      
        <div data-role="page" data-theme="b" id="orderpage">
            <div data-role="fieldcontain">
                   <div data-theme="b"  align="center">
                       <img src="{!URLFOR($Resource.Apttussf)}" alt="Smiley face" />
                       
                       <div style="background:white; color:#7BC043; text-align: center;"><h3 style="font-size: 18px;">Please enter your first name below, then hit <strong>Sizes</strong> to begin configuring your Tshirt!</h3>
                       </div>
                           
                   </div>
                   <div style="padding:10px;" data-theme="b" align="center">
                       <br/>
                   <input  name="ordername" id="ordername" placeholder="Please enter your first name here." required="true" style="background:white; color:black;"/>
                   <input id="OrderId" name="OrderId" type="hidden" />
                   </div>
                   <div id="ordermsg" style="display:none;" class="ordermsgclass msg-class"> Please enter your order name.</div>
                   <div id="sizeselectmsg" style="display:none;" class="sizeselectmsgclass msg-class"> Please select sizes first.</div>
                   <div id="colorselectmsg" style="display:none;" class="colorselectmsgclass msg-class"> Please select your color first.</div>
            </div>        
            <ul data-role="listview" data-theme="b" data-dividertheme="b">
                    <li style="background:white; color:#7BC043;border-top-width: 1px;"><a href="javascript:void(0);" onclick="checkName('sizeselection');" style="background:#7bc043; color:white;">Sizes</a></li>
                    <li style="background:white; color:#7BC043;border-top-width: 1px;"><a href="javascript:void(0);" onclick="checkName('colorselection');" style="background:#7bc043; color:white;">Colors</a></li>
                    <li style="background:white; color:#7BC043;border-top-width: 1px;"><a href="javascript:void(0);" onclick="checkName('sloganselection');" style="background:#7bc043; color:white;">Slogans</a></li>                   
                   <div data-theme="b"  align="center">
                       <br/>             
                       <br/>
                       <img src="{!URLFOR($Resource.cover)}" alt="Smiley face" />
                   </div>
                <br/>
            </ul>
            <a href='#homepage'  id="homepageId" class='ui-btn-center' data-role="button" data-direction="reverse" data-transition="flip" style="background:#7bc043; color:white;">Home</a>
            
        </div>
      
          
        <div data-role="page" data-theme="b" id="sizeselection">
            <div data-theme="b"  align="center">
               <img src="{!URLFOR($Resource.Apttussf)}" alt="Smiley face" />
           </div>
            
             <ul data-role="listview">
                    <li style="background:white; color:#7BC043; text-align: center;"><h3 style="font-size: 18px;">What shirt size would you like?</h3></li>
                    <li style="background:white; color:#7BC043;">
                    
                        <fieldset class="myhover" data-role="controlgroup" data-iconpos="right" >                              
                            
                                
                            
                            <input type="radio" name="radio-size" id="radio-size-one" value="Small" class="sizeclass" style="background:white; color:#7BC043;"/>
                            <label  for="radio-scoop-one" style="background:#7bc043; color:white;text-align: center;">Small</label>
                            
                        </fieldset>
                    
                    </li>
                 
                 
                    <li style="background:white; color:#7BC043;">
                    
                        <fieldset class="myhover" data-role="controlgroup" data-iconpos="right" >
                            <input type="radio" name="radio-size" id="radio-size-two" value="Medium" class="sizeclass" style="background:white; color:#7BC043;"/>
                            <label  for="radio-size-two" style="background:#7bc043; color:white;text-align: center;">Medium</label>
                        </fieldset>
                    
                    </li>
                
                	 <li style="background:white; color:#7BC043;">
                    
                        <fieldset class="myhover" data-role="controlgroup" data-iconpos="right" >
                            <input type="radio" name="radio-size" id="radio-size-three" value="Large" class="sizeclass" style="background:white; color:#7BC043;"/>
                            <label  for="radio-size-four" style="background:#7bc043; color:white;text-align: center;">Large</label>
                        </fieldset>
                    
                    </li>
                
                	 <li style="background:white; color:#7BC043;">
                    
                        <fieldset class="myhover" data-role="controlgroup" data-iconpos="right" >
                            <input type="radio" name="radio-size" id="radio-size-four" value="X-Large" class="sizeclass" style="background:white; color:#7BC043;"/>
                            <label  for="radio-size-four" style="background:#7bc043; color:white;text-align: center;">X-Large</label>
                        </fieldset>
                    
                    </li>
            </ul>
            <a href='javascript:void(0);' id="colorselectionId" class='ui-btn-center' data-role="button" data-direction="reverse" data-transition="flip" style="background:#7bc043; color:white;" onclick="checksize();">Next</a>
            <a href='#orderpage' id="backtoorderId" class='ui-btn-center' data-role="button" data-direction="reverse" data-transition="flip" style="background:#7bc043; color:white;" onclick="hideallmsgs();">Back</a>
            <div id="sizeselectmsgonsize" style="display:none;" class="sizeselectmsgclass msg-class"> Please select what size shirt you would like.</div>
            <div data-theme="b"  align="center">
                <br/>
                <br/>
                <br/>
                <br/>
                <br/>
            
               <img src="{!URLFOR($Resource.sizechart)}" alt="Smiley face" />
           </div>
        </div>
        
        <div data-role="page" data-theme="b" id="flavorsselection">   
            <div data-theme="b"  align="center">
               <img src="{!URLFOR($Resource.Apttussf)}" alt="Smiley face" />
                
           </div>   
            
            <ul data-role="listview">
                <li style="background:white; color:#7BC043; text-align: center;"><h3 style="font-size: 18px;">What color would you like?</h3></li>
                    <li style="background:white; color:#7BC043;">
                    
                        <fieldset class="myhover" data-role="controlgroup" data-iconpos="right" >
                            <input type="checkbox" name="checkbox-flavor" id="checkbox-color-one" value="Gray" class="colorclass1"  style="background:white; color:#7BC043;" onclick="colorimagedisplay();"/>
                            <label  for="checkbox-color-one" style="background:#7bc043; color:white;text-align: center;">Gray</label>
                        </fieldset>
                    
                    </li>
                    <li style="background:white; color:#7BC043;">
                    
                        <fieldset class="myhover" data-role="controlgroup" data-iconpos="right" >
                            <input type="checkbox" name="checkbox-color" id="checkbox-color-two" value="Blue" class="colorclass2"  style="background:white; color:#7BC043;" onclick="colorimagedisplay();" />
                            <label  for="checkbox-color-two" style="background:#7bc043; color:white;text-align: center;">Blue</label>
                        </fieldset>
                    
                    </li>                  
                  
            </ul>
            <a href='javascript:void(0);' id="sloganselectionId" class='ui-btn-center' data-role="button" data-direction="reverse" data-transition="flip" onclick="checkmax();" style="background:#7bc043; color:white;">Next</a>
            <a href='#sizeselection' id="backtosizeId" class='ui-btn-center' data-role="button" data-direction="reverse" data-transition="flip" style="background:#7bc043; color:white;" onclick="hideallmsgs();">Back</a>
            <div id="ordermsg" style="display:none;" class="flavormsgclassone msg-class"> You can only select one color.</div>          
            <div id="ordermsg" style="display:none;" class="flavormsgclassnone msg-class"> Please select atleast one color.</div>  
            <div data-theme="b"  align="center">
                <br/>
                
               <img src="{!URLFOR($Resource.greyshirt)}" alt="Smiley face" class="checkbox-color-one-image" style="display:none;"/>
               <img src="{!URLFOR($Resource.blueshirt)}" alt="Smiley face" class="checkbox-color-two-image" style="display:none;"/>
               
               
           </div>
        </div>
        
        <div data-role="page" data-theme="b" id="sloganselection">   
            <div data-theme="b"  align="center">
                
               <img src="{!URLFOR($Resource.Apttussf)}" alt="Smiley face" />
                
                
           </div>   
            
                                   
            <ul data-role="listview">
                    <li style="background:white; color:#7BC043; text-align: center;"><h3 style="font-size: 18px;">Would slogan would you like?</h3></li>
                    <li style="background:white; color:#7BC043;">
                    
                        <fieldset class="myhover" data-role="controlgroup" data-iconpos="right" >
                            <input type="checkbox" name="checkbox-h-6a" id="checkbox-slogan-one" class="sloganclass1" onclick="sloganimagedisplay();"/>
                            <label  for="checkbox-slogan-one" style="background:#7bc043; color:white;text-align: center;">Nice Saas</label>
                        </fieldset>
                    
                    </li>
                    <li style="background:white; color:#7BC043;">
                    
                        <fieldset class="myhover" data-role="controlgroup" data-iconpos="right" >
                            <input type="checkbox" name="checkbox-h-6a" id="checkbox-slogan-two" class="sloganclass2" onclick="sloganimagedisplay();"/>
                            <label  for="checkbox-slogan-two" style="background:#7bc043; color:white;text-align: center;">Total SaaS-ifaction</label>
                        </fieldset>
                    
                    </li>
                    
            </ul>
            
             <input type="hidden" id="OrderId" />          
            <a id="placeorder" class='ui-btn-center' data-role="button" data-direction="reverse" data-transition="slidefade" style="background:#7bc043; color:white; ">Order</a>
            
            <a href='#colorselection' id="backtocolorId" class='ui-btn-center' data-role="button" data-direction="reverse" data-transition="flip" style="background:#7bc043; color:white;" onclick="hideallmsgs();">Back</a>
           
                <br/>
            
           </div>
            <br/>
            <br/>
          <div  data-theme="b" align="center">
             <img src="{!URLFOR($Resource.SaaS)}" alt="Smiley face" class="checkbox-slogan-one-image" style="display:none;"/>
               <img src="{!URLFOR($Resource.SasSifaction)}" alt="Smiley face" class="checkbox-slogan-two-image" style="display:none;"/>               
            </div>
        
        
    </body> 
    </form>    
</apex:page>
