<div style="display: flex; align-items: center;">
  <img src="/img/higher_resolution_img (2).jpg" alt="Alt text" style="width: 115px; margin-right: 20px; border-radius: 10px; box-shadow: 5px 5px 15px rgba(0, 0, 0, 0.5);">
  <div style="font-family: Arial, sans-serif; font-size: 40px;">
    <span>InsightMonitor</span>
    <span style="color: #888B8D;"> Documentation: Setup</span>
  </div>
</div>

&nbsp;

## <div style="font-family: Arial, sans-serif; font-size: 40px; color: #888B8D;">Setup Authorization Environment</div>

**Key Terminology**

- **Provider**: The entity (ModelzInsights) responsible for storing the secrets in AWS Secrets Manager.
- **Client**: The entity needing access to the secrets stored by the Provider, in this case running an EC2 instance in their own AWS account.  

---

#### <p class="text-primary" style="font-weight: bold;">I. Accessing Provider's Secrets from Client's AWS EC2</p>

This section explains the process for configuring access to a Provider's AWS Secrets Manager from a Client's EC2 instance. It involves creating IAM roles and policies in both the Provider's and Client's AWS accounts to ensure secure and efficient retrieval of secrets.

**Introduction**

The Provider stores the secret and grants the Client temporary access to retrieve it from AWS Secrets Manager through the Client's EC2 instance. This process involves the following steps:

1. **Set up IAM roles and policies** to enable secure cross-account access.
2. **Configure AWS Secrets Manager** for shared access between accounts.
3. **Retrieve the secret** using ModelzInsight's application from the Client's EC2 instance.  

<p  style="font-size: 14px; color: #4682B4;">Note: This is the only scenario where the Python Dash app will access an external AWS account.</p>   

<p class="text-primary" style="font-weight: bold;">Provider Setup Steps</p>
<p>
The Provider needs to create a secret, along with the required policies and roles, to facilitate secure cross-account access. Although the Client does not need to perform this setup, it is provided here to give the Client a clear understanding of the secure authorization process.
</p>

<!-- Ellipsis as the toggle button -->
<span onclick="toggleVisibility('stepsContent')" style="color: blue; cursor: pointer;">&#x2026; Show Steps</span>

<!-- Steps are initially hidden inside this div -->
<div id="stepsContent" style="display:none; margin-top: 10px;">

    <p><strong>Step 1: Create Client-specific Authorization Secret</strong></p>
    <ul>
        <li>Go to <code>Secrets Manager</code> and create a new Secret.</li>
        <li><code>Secret Type = Other type of secret</code></li>
        <li>Input the relevant value pairs</li>
        <li>Enter a secret name, description.</li>
        <li>Hit next until you can <code>Store</code> the secret.</li></li>
    </ul>

    <p><strong>Step 2: Create an IAM Policy</strong></p>
    <ul>
        <li>From IAM, create a policy.</li>
        <li><code>Service = Secrets Manager</code>. Hit Next</li>
        <li>Copy the permission statement below into the JSON Policy Editor in the <code>Specify permissions</code> section.</li>
    </ul>

    <!-- JSON #1: WITH Copy Button -->
    <ol type="a" style="list-style-type:none;">
        <li style="margin:0; padding:0;">
            <div style="display: flex; justify-content: space-between; align-items: center; margin-top: 20px;">
                <span></span> 
                <button onclick="copyToClipboard('jsonCode1')" 
                        style="background-color: rgba(100, 149, 237, 0.6); color: white; border: 2px solid blue; padding: 6px 20px; cursor: pointer; border-radius: 4px; font-size: 14px;">
                    Copy
                </button>
            </div>
            <pre style="margin-top: 5px;"><code class="json" id="jsonCode1">
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "secretsmanager:GetSecretValue"
            ],
            "Resource": [
                "arn:aws:secretsmanager:&lt;region&gt;:&lt;PROVIDER-account-id&gt;:secret/&lt;secret-id&gt;"
            ]
        }
    ]
}
            </code></pre>
        </li>
    </ol>

    <ul>
        <li>In another tab, go to Secrets Manager and get the ARN. Input it into the Resource section of the permission statement. Hit Next.</li>
        <li>Enter a policy name, description, and check Secrets Manager to ensure proper input.</li>
        <li>Hit <code>Create Policy</code>.</li>
    </ul>

    <p><strong>Step 3: Create an IAM Role for Cross-Account Access</strong></p>
    <p>Now you need to create an IAM role that the customer's account can assume, with the policy created in the previous step attached.</p>
    <ol>
        <li>Create a new role in the IAM console of the owner's account.</li>
        <li>Select <code>Another AWS account </code> as the trusted entity type.</li>
        <li>Enter the  <code>Customer AWS Account ID </code> as the trusted account.</li>
        <li>Attach the policy created in Step 1 to this role.</li>
        <li>Give the role a name, e.g.,  <code>CustomerSecretsAccessRole </code>.</li>
        <li>Hit <code>Create Role</code></li>
    </ol>
<p style="color: #a9a9a9;">You should see the below JSON in the Trust relationships section:  </p>
    <!-- JSON #2: WITHOUT Copy Button (Just show the JSON) -->
    <pre style="margin-top: 5px;"><code class="json">  
    
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::&lt;CUSTOMER-account-id&gt;:root"
            },
            "Action": "sts:AssumeRole"
        }
    ]
}
    </code></pre>

    <p><strong>Step 4: Allow Cross-Account Access in Secrets Manager Resource Policy</strong></p>
    This step creates a Resource permission such that only the role has access to the secret.
    <ul>
        <li>In the Secrets Manager console, navigate to the secret you just created.</li>
        <li>Under Resource permissions, copy the below JSON</li>
        <li>Enter the Secret arn in the Resource section.  This isn't technically needed, but each permission requires a Resource.  </li>
        <li>Under the PrincipalArn enter the PROVIDER-account's role created in step 3.</li>
    </ul>

    <!-- JSON #3: WITH Copy Button -->
    <ol type="a" style="list-style-type:none;">
        <li style="margin:0; padding:0;">
            <div style="display: flex; justify-content: space-between; align-items: center; margin-top: 20px;">
                <span></span> 
                <button onclick="copyToClipboard('jsonCode3')" 
                        style="background-color: rgba(100, 149, 237, 0.6); color: white; border: 2px solid blue; padding: 6px 20px; cursor: pointer; border-radius: 4px; font-size: 14px;">
                    Copy
                </button>
            </div>
            <pre style="margin-top: 5px;"><code class="json" id="jsonCode3">
{
  "Version" : "2012-10-17",
  "Statement" : [ {
    "Effect" : "Deny",
    "Principal" : "*",
    "Action" : "secretsmanager:GetSecretValue",
    "Resource" : "arn:aws:secretsmanager:&lt;region&gt;:&lt;PROVIDER-account-id&gt;:secret/&lt;secret-id&gt;",
    "Condition" : {
      "StringNotEquals" : {
        "aws:PrincipalArn" : "arn:aws:iam::&lt;PROVIDER-account-id&gt;:role/&lt;ROLE-NAME&gt;"
      }
    }
  } ]
}
            </code></pre>
        </li>
    </ol>

</div>  


<p class="text-primary" style="font-weight: bold;">Client Setup Steps</p>  
The Client must create a(n):  

* Policy to assume the Provider's role (see Provider instructions)  
* IAM Role for the EC2 housing the ModelzInsights App  
* Attach the role to the EC2  

<p><strong>Step 1: Create an IAM Policy to Assume Owner's Role</strong></p>
First, the Client needs to receive the `Resource arn to the Provider role` from the Provider (from Provider step 3).  This will enable Client to assume the role using <strong> STS </strong> granting temporary access to the secret.

* In the IAM console, create a policy.
* `Service = STS`
* Paste the following JSON into the `Specify permissions` field.
* Provide the Resource ARN for the Provider's role (this will be supplied by the Provider).

<!-- Customer #1 Button: WITH Copy Button -->
<ol type="a" style="list-style-type:none;">
    <li style="margin:0; padding:0;">
        <div style="display: flex; justify-content: space-between; align-items: center; margin-top: 20px;">
            <span></span> 
            <button onclick="copyToClipboard('Customer1')" 
                    style="background-color: rgba(100, 149, 237, 0.6); color: white; border: 2px solid blue; padding: 6px 20px; cursor: pointer; border-radius: 4px; font-size: 14px;">
                Copy
            </button>
        </div>
        <pre style="margin-top: 5px;"><code class="json" id="Customer1">
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "sts:AssumeRole",
            "Resource": "arn:aws:iam::&lt;PROVIDER-account-id&gt;:role/&lt;ROLE-NAME&gt;"
        }
    ]
}
        </code></pre>
    </li>
</ol>  

<p><strong>Step 2: Create a role in the Customer’s account for the EC2 instance: </strong></p>  

* `Select "AWS Service` as the trusted entity.  
* `Select EC2` for the Use case.  Hit Next.
* Attach the policy from `Step 1`.  Hit Next.
* Give the role a name, description, and ensure the Selected trusted entity is similar to:

<ol type="a" style="list-style-type:none;">
    <li style="margin:0; padding:0;">
        <pre style="margin-top: 5px;"><code class="json" >
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "sts:AssumeRole"
            ],
            "Principal": {
                "Service": [
                    "ec2.amazonaws.com"
                ]
            }
        }
    ]
}
        </code></pre>
    </li>
</ol>  

* Ensure the policy attached is present in `Step 2: Add permissions` section.  
* Hit <code>Create Role</code></li>  

<p><strong>Step 3: Attach Role to EC2: </strong></p> 
When launching or modifying the EC2 instance, attach the role created in Step 2.  If the modifying the EC2:  

* Go to `Actions` -> `Security` -> `Modify IAM Role`
* Select the role from Step 2 and hit `Update IAM role`.


#### <p class="text-primary" style="font-weight: bold;">II. Accessing Provider's Secrets from Local Environment (with an AWS account)</p>








































<script>
    function toggleVisibility(id) {
        var elem = document.getElementById(id);
        if (elem.style.display === "none") {
            elem.style.display = "block";
            event.target.innerText = '... Hide Steps';
        } else {
            elem.style.display = "none";
            event.target.innerText = '... Show Steps';
        }
    }

    function copyToClipboard(jsonId) {
        var content = document.getElementById(jsonId).innerText;
        navigator.clipboard.writeText(content).then(function() {
            alert('Copied to clipboard!');
        }, function(err) {
            alert('Failed to copy: ', err);
        });
    }
</script>

