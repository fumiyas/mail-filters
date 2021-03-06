#!/usr/bin/make -f
##
## Postfix: Makefile to update *.db files
##
## Copyright (c) 2014 SATOH Fumiyasu @ OSS Technology Corp., Japan
##               <https://github.com/fumiyas/mail-filters/blob/master/postfix/Makefile>
##               <https://fumiyas.github.io/>
##
## License: Do What The Fuck You Want To Public License (WTFPL) version 2
##

POSTFIX_CONFIG_DIR=	/etc/postfix
POSTCONF=		/usr/sbin/postconf
POSTMAP=		/usr/sbin/postmap

default:
	@cd $(POSTFIX_CONFIG_DIR) && $(MAKE) db

.PHONY: default db

db: Makefile.postmap
	@echo 'Updating *.db files ...'
	@$(MAKE) -f $(POSTFIX_CONFIG_DIR)/Makefile.postmap

Makefile.postmap: Makefile main.cf
	@echo 'Updating $@ ...'
	@set -e; \
	tmp="$@.$$$$.tmp"; \
	trap 'rm -f "$$tmp"' EXIT; \
	rm -f "$$tmp"; \
	echo 'POSTMAP=$(POSTMAP)' >>"$$tmp"; \
	echo '.PHONY: db' >>"$$tmp"; \
	echo 'db::' >>"$$tmp"; \
	{ $(POSTCONF) -c $(POSTFIX_CONFIG_DIR) -h || kill $$$$; } \
	|sed -n 's/, */ /g; /hash:/p' \
	|tr ' ' '\n' \
	|sed -n \
	  -e 's#^hash:\$$config_directory/#$(POSTFIX_CONFIG_DIR)/#p' \
	  -e 's#^hash:$(POSTFIX_CONFIG_DIR)/#$(POSTFIX_CONFIG_DIR)/#p' \
	|sort -u \
	|while read mapfile; do \
	  echo "db:: $$mapfile.db"; \
	  echo "$$mapfile.db: $$mapfile"; \
	  echo "	\$$(POSTMAP) $$mapfile"; \
	done \
	>>"$$tmp"; \
	mv "$$tmp" $@

