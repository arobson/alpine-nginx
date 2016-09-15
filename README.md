## alpine-nginx
Extends the official NGiNX image with a custom build of [ngx_upstream_jdomain](https://github.com/wdaike/ngx_upstream_jdomain) from [my fork](https://github.com/arobson/ngx_upstream_jdomain).

### Motivation
We currently use NGiNX between AWS's ELB and our microservices for:
 * SSL termination
 * static resource caching
 * compression
 * other stuff NGiNX is good at ...

Instead of producing static config files that map service IPs to upstream blocks, we use kubernetes service names. In kubernetes, services are registered with skyDNS. Multiple Pod IPs can be assigned and these assignments can change over time. NGiNX only resolves these DNS names at start-up. 

By adding this module to NGiNX, incoming traffic from an ELB -> NGiNX -> Service should continue to resolve to the correct service IPs over time even as they change without having to regenerate configuration files or restart the NGiNX container(s).

### Module Configuration

**Examples:**

#### defaults to port 80
```
  upstream backend {                                                                              
    jdomain myServiceName.namespace;
  }                                                              
```

#### custom port
```
upstream backend {                                                                              
    jdomain myServiceName.namespace port 8080;
}
```                        

#### Arguments

  * Syntax: jdomain <domain-name> [port=80] [max_ips=20] [interval=1] [retry_off]
  * Context:    upstream                                                                          
  * port:       Backend's listening port.                                                         
  * max_ips:    IP buffer size.
  * interval:   How many seconds to resolve domain name.
  * retry_off:  Do not retry if one IP fails. 

See https://www.nginx.com/resources/wiki/modules/domain_resolve/ for details.

## Acknowledgements

### `ngx_upstream_jdomain` - Original Author
wdaike <wdaike@163.com>, Baidu Inc.

### `ngx_upstream_jdomain` - Copyright & License

This module is licenced under the BSD License.
Copyright (C) 2014-2014, by wdaike <wdaike@163.com>, Baidu Inc.

All rights reserved.

Redistribution and use in source and binary forms, with or without modification, are permitted provided that the following conditions are met:

* Redistributions of source code must retain the above copyright notice, this list of conditions and the following disclaimer.

* Redistributions in binary form must reproduce the above copyright notice, this list of conditions and the following disclaimer in the documentation and/or other materials provided with the distribution.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

### Other Contributions

Thanks to [Oliver Poitrey's](https://github.com/rs) for his PRs/improvements to this module.