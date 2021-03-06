ifeq ($(CONFIG_ARCH_MSM8917),y)
five_ta_dir := five/msm8917
else
five_ta_dir := five
endif

# FIVE TA
five_fwlist := \
    $(five_ta_dir)/five.mdt \
    $(five_ta_dir)/five.b00 \
    $(five_ta_dir)/five.b01 \
    $(five_ta_dir)/five.b02 \
    $(five_ta_dir)/five.b03 \
    $(five_ta_dir)/five.b04 \
    $(five_ta_dir)/five.b05 \
    $(five_ta_dir)/five.b06

five_fwdir := firmware/five
five_signed_fwdir := firmware/five/signed
five_objs := $(addprefix $(obj)/,$(five_fwlist))

ifeq ($(CONFIG_FIVE_SIGN_TA),y)
# Setup five_sign_runtype and five_sign_model
include $(srctree)/firmware/five/sign_mode.mk

# The strings are came from Kconfig are quoted with \". This symbol must be removed
# because 'make' doesn't do that automatically
five_pilsplitter := $(subst $\",,$(CONFIG_FIVE_PILSPLITTER_PATH))
five_signclient := $(subst $\",,$(CONFIG_FIVE_SIGNCLIENT_PATH))

$(five_objs): $(five_fwdir)/five.signed

$(five_fwdir)/five.signed: $(five_pilsplitter) $(five_signed_fwdir)/five.mbn
	python $(five_pilsplitter) $(five_signed_fwdir)/five.mbn $(five_fwdir)/five
	touch $@

$(five_signed_fwdir)/five.mbn: $(five_signclient) $(srctree)/firmware/five/five.mbn
	mkdir -p $(five_signed_fwdir)
	java -jar $(five_signclient) -model $(five_sign_model) \
		-runtype $(five_sign_runtype) -input $(srctree)/firmware/five/five.mbn \
		-output $(five_signed_fwdir)/five.mbn

five_fwabs := $(addprefix $(objtree)/,$(filter-out /%,$(fwdir)))$(filter /%,$(fwdir))

$(patsubst %,$(obj)/%.gen.S, $(five_fwlist)): %: $(wordsize_deps)
	$(call cmd,fwbin,$(five_fwabs)/$(patsubst $(obj)/%.gen.S,%,$@))

# The .o files depend on the binaries directly; the .S files don't.
$(patsubst %,$(obj)/%.gen.o, $(five_fwlist)): $(obj)/%.gen.o: $(fwdir)/%

obj-y += $(patsubst %,%.gen.o, $(five_fwlist))

clean-files += $(five_objs) $(five_fwdir)/five.signed
clean-dirs += $(five_signed_fwdir)

else # CONFIG_FIVE_SIGN_TA

ifneq ($(fwdir),)
fw-external-$(CONFIG_FIVE_TEE_DRIVER) += $(five_fwlist)
else
fw-shipped-$(CONFIG_FIVE_TEE_DRIVER) += $(five_fwlist)
endif

endif # CONFIG_FIVE_SIGN_TA
