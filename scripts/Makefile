.PHONY: test gen-whitelist sync

# Run whitelist filter tests
test:
	python3 whitelist_filter.py
	@echo "Tests passed."

# Generate master whitelist from manual entries and external sources
gen-whitelist:
	python3 gen-whitelist.py
	@echo "Master whitelist generated."

# Sync mirrors (depends on tests passing and updated whitelist)
sync: test gen-whitelist
	./sync-mirrors.sh
