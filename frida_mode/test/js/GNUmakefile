PWD:=$(shell pwd)/
ROOT:=$(shell realpath $(PWD)../../..)/
BUILD_DIR:=$(PWD)build/
TEST_DATA_DIR:=$(BUILD_DIR)in/
TEST_DATA_FILE:=$(TEST_DATA_DIR)in

TESTINSTBIN:=$(BUILD_DIR)test
TESTINSTSRC:=$(PWD)test.c

TESTINSTBIN2:=$(BUILD_DIR)test2
TESTINSTSRC2:=$(PWD)test2.c

QEMU_OUT:=$(BUILD_DIR)qemu-out
FRIDA_OUT:=$(BUILD_DIR)frida-out

.PHONY: all 32 clean qemu frida

all: $(TESTINSTBIN) $(TESTINSTBIN2)
	make -C $(ROOT)frida_mode/

32:
	CFLAGS="-m32" LDFLAGS="-m32" ARCH="x86" make all

$(BUILD_DIR):
	mkdir -p $@

$(TEST_DATA_DIR): | $(BUILD_DIR)
	mkdir -p $@

$(TEST_DATA_FILE): | $(TEST_DATA_DIR)
	echo -n "000" > $@

$(TESTINSTBIN): $(TESTINSTSRC) | $(BUILD_DIR)
	$(CC) $(CFLAGS) $(LDFLAGS) -o $@ $<

$(TESTINSTBIN2): $(TESTINSTSRC2) | $(BUILD_DIR)
	$(CC) $(CFLAGS) $(LDFLAGS) -o $@ $<

clean:
	rm -rf $(BUILD_DIR)

frida_js_entry: $(TESTINSTBIN) $(TEST_DATA_FILE)
	AFL_FRIDA_JS_SCRIPT=entry.js \
	$(ROOT)afl-fuzz \
		-D \
		-O \
		-i $(TEST_DATA_DIR) \
		-o $(FRIDA_OUT) \
		-- \
			$(TESTINSTBIN) @@

frida_js_replace: $(TESTINSTBIN) $(TEST_DATA_FILE)
	AFL_FRIDA_JS_SCRIPT=replace.js \
	$(ROOT)afl-fuzz \
		-D \
		-O \
		-i $(TEST_DATA_DIR) \
		-o $(FRIDA_OUT) \
		-- \
			$(TESTINSTBIN) @@

frida_js_patch: $(TESTINSTBIN2) $(TEST_DATA_FILE)
	AFL_FRIDA_JS_SCRIPT=patch.js \
	$(ROOT)afl-fuzz \
		-D \
		-O \
		-i $(TEST_DATA_DIR) \
		-o $(FRIDA_OUT) \
		-- \
			$(TESTINSTBIN2) @@

frida_js_stalker: $(TESTINSTBIN2) $(TEST_DATA_FILE)
	AFL_FRIDA_JS_SCRIPT=stalker.js \
	$(ROOT)afl-fuzz \
		-D \
		-O \
		-i $(TEST_DATA_DIR) \
		-o $(FRIDA_OUT) \
		-- \
			$(TESTINSTBIN2) @@
