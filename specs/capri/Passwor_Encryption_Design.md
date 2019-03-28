# Title

**Author(s)**: [Jack Li](https://github.com/lasshpanzer)

## Summary

This document is a design proposal to provide a password security mechanism for OpenSDS.

## Motivation

Currently, the password used by other components such as cinder, kubernetes, MultiCloud to interact with OpenSDS is not encrypted, which means the user’s information can be leaked easily.

### Goals

The password shall be encrypted when other components use it to interact with OpenSDS.

1. A default encryption tool will be provided for users to obtain the cipher text.

2. Which encryption tool is selected and whether the password is encrypted can be configurable. By default, the encryption tool is AES which is an open source tool, and the password is not encrypted.


## Design Details

1. Add options to the OpenSDS configuration file that enable user to choose the password encrypter tool they want and choose whether to encrypt the password. The encryption tools can be implemented by the user. These two options added to the opensds.conf are as follows:

```
// Whether to encrypt the password. If enabled, the value of the password must be cipher text.
enable_encrypted = False
// Encryption and decryption tool. Default value is aes. The decryption tool can only decrypt the corresponding ciphertext.
pwd_encrypter = aes
```

Calling example in code:

```
    // Get the cipher text of the password	
    var pwdCiphertext = opt.Password
    if opt.EnableEncrypted {
        // Instantiate an encryption tool
        pwdTool := pwd.NewPwdEncrypter(opt.PwdEncrypter)
        // Decrypt the password and obtain the original password.
        password, err := pwdTool.Decrypter(pwdCiphertext)
        if err != nil {
	        return nil, err
        }
        pwdCiphertext = password
    }
    opt.Password = pwdCiphertext
```

2. Provide OpenSDS default AES password encryption tool, so that user can use this tool to get cipher text and shell scripts can call it for automatic encryption during the deployment process. 

AES encryption tool user guide：
build/out/bin/pwdEncrypter password

3. Provide a unified code framework for encryption and decryption that enables user to implement their own encryption tool.

```
type PwdEncrypter interface {
	Encrypter(password string) (string, error)
	Decrypter(code string) (string, error)
}
```

### Data model impact

Add two attributes PwdEncrypter and EnableEncrypted in AuthOptions struct.

```
type AuthOptions struct {
	Strategy        string `yaml:"Strategy"`
	AuthUrl         string `yaml:"AuthUrl,omitempty"`
	DomainName      string `yaml:"DomainName,omitempty"`
	UserName        string `yaml:"UserName,omitempty"`
	Password        string `yaml:"Password,omitempty"`
	PwdEncrypter    string `yaml:"PwdEncrypter,omitempty"`
	EnableEncrypted bool   `yaml:"EnableEncrypted,omitempty"`
	TenantName      string `yaml:"TenantName,omitempty"`
}
```

### REST API impact

None

### Security impact

Increased user information security.

### Other end user impact

None

### Performance impact

Note:These parts of hotpot should be considered encrypted:
Southbound volume drivers or other drivers: multi-cloud, dorado, fusionstorage, cinder. These parts use user name and password to connect with hotpot.
Northbound plugin:csi. These parts use user name and password to get token from keystone.
 

### Other deployer impact

When multi-cloud, dorado, cinder, kubernetes, keystone connect with the OpenSDS, user needs to change the value of enable_encrypted and pwd_encrypter in the configuration file to decide whether to encrypt the password and choose which encryption tool to use or keep the default one.

### Developer impact

Developers can implement their own encryption and decryption functions in the OpenSDS encryption framework based on their requirements. If there are new parts added to hotpot, you should consider whether to encrypt password.

## Use Cases

Before deployment, user change EnableEncrypted to true and keep PwdEncrypter option as default in the configuration file, then use aes encryption tool get cipher text and write it to the password option.

## Implementation

1. Implement the encryption tool framework.
2. Implement AES encryption and decryption functions.
3. Implement AES encryption CLI tool.
4. Modify multi-cloud, dorado, cinder, kubernetes and keystone configuration file, AuthOptions struct and corresponding code.

## Alternatives considered

None

## Open issues

None
