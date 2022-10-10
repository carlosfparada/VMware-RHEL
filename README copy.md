# Fortinet

# ServiceNow basic

https://aap2.demoredhat.com/demos/servicenow/2-closed_loop_incident_mgmt/#step-5---setup-servicenow-rest-message


# ServiceNow Oauth

https://www.ansible.com/blog/ansible-servicenow-howto-part-3-making-outbound-restful-api-calls-to-ansible-tower

Client ID: xxxx

Client secret: xxxx


Your instance URL: https://devxxxxxx.service-now.com

Username: admin
Current password: xxxxxxxxxx


## Service Now
Need to allow self-signed certificates on REST message

Problem Fix #1 
Search All menus: 'sys_properties.list'
Set
com.glide.communications.trustmanager_trust_all	= true
com.glide.communications.httpclient.verify_hostname	= false

## AAP Certificate
Need to allow add alternate IPs with the public IP

Problem Fix #2
cd /home/redhat/
File.conf:
[req]
default_bits       = 2048
distinguished_name = req_distinguished_name
req_extensions     = v3_req
prompt = no
[req_distinguished_name]
countryName                = US
stateOrProvinceName        = NewYork
localityName               = NewYork
organizationName           = Red Hat
commonName                 = aap1-ctrl1.my-lab.local
[v3_req]
subjectKeyIdentifier = hash
basicConstraints = CA:TRUE
subjectAltName = @alt_names
[alt_names]
IP.1 = 168.119.66.81
IP.2 = 192.168.1.21
DNS.1 = aap1-ctrl1.my-lab.local

create CA: openssl genrsa -des3 -out ca.key
create cert: openssl req -new -x509 -days 3650 -key ca.key -out tower.crt -config file.conf -extensions v3_req
remove pass from cert: openssl rsa -in ca.key -out tower.key
query ssl cert: openssl x509 -in tower.crt -text

cp tower.crt /etc/tower/
cp tower.key /etc/tower/
systemctl restart nginx



Script

 try { 
 var r = new sn_ws.RESTMessageV2('Fortinet deployment', 'AAP');

//override authentication profile 
//authentication type ='basic'/ 'oauth2'
//r.setAuthenticationProfile(authentication type, profile name);

//set a MID server name if one wants to run the message on MID
//r.setMIDServer('MY_MID_SERVER');

//if the message is configured to communicate through ECC queue, either
//by setting a MID server or calling executeAsync, one needs to set skip_sensor
//to true. Otherwise, one may get an intermittent error that the response body is null
//r.setEccParameter('skip_sensor', true);

 var response = r.execute();
 var responseBody = response.getBody();
 var httpStatus = response.getStatusCode();
}
catch(ex) {
 var message = ex.message;
}