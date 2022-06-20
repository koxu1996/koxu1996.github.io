## Node Operators Frequently Asked Questions 

1. **Q:** Can a validator node change IP address once it is already working for a while?

   **A:** Yes. It can shutdown and do this. But it needs to update the IP in all /etc/casper/_\<semver\>_/config.toml files. If it doesn't change the config.toml files it is announcing the wrong IP for nodes to connect back to it and it is isolated and doesn't progress.


Other FAQs here: https://docs.casperlabs.io/faq/faq-validator/