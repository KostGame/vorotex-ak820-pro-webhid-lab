# Research notes

## Confirmed for the tested VOROTEX AK820 PRO

- Product name exposed through WebHID: `AK820 PRO`.
- Wired VID/PID: `258A:019D`.
- WebHID can open the device through Chromium.
- Vendor-defined collection `FF02:0002` is accessible.
- Feature Report 9 is present and has the same 519-byte descriptor size used by SuperFrame Phantom tooling.
- Additional Feature Reports 5 and 6 are present.
- Phantom Editor can connect to the keyboard, but its UI/layout is for the Phantom device and does not expose the AK820 TFT or encoder configuration.
- EPOMAKER Hub for Cypher 81 does not offer the tested AK820 PRO in its device picker.

## Official VOROTEX software observations

Static analysis of the official VOROTEX package indicates:

- the configurator belongs to the OEM/BYCOMBO4 software family;
- model-specific configuration identifies the keyboard as an 81-key layout with encoder and 128×128 TFT;
- the configuration model exposes Base/Default, FN1, FN2 and Tap-style layers/modes;
- the application uses standard Windows HID APIs rather than a dedicated kernel driver for ordinary configuration;
- display-related functionality exists in the OEM application, including image/GIF-oriented code paths.

Raw vendor binaries are intentionally not stored in this repository.

## Strong donor: SuperFrame Phantom

The tested AK820 PRO and the open SuperFrame Phantom tooling share the following WebHID transport signature:

- VID `0x258A`;
- PID `0x019D`;
- vendor Usage Page `0xFF02`;
- Usage `0x0002`;
- Feature Report ID `0x09`;
- 519-byte Feature Report payload shape.

The Phantom Editor successfully opens the AK820 PRO in Chromium. This is strong evidence of a shared lower-level USB/HID stack.

However, this does **not** prove that the two keyboards have the same:

- physical matrix;
- keymap region layout;
- flash sector layout;
- firmware image;
- TFT/encoder protocol.

Do not use Phantom Editor Save/Apply on AK820 PRO until write mapping and recovery are proven.

## Other donor families

### AULA / BYCOMBO4

AULA-family reverse engineering remains valuable because it documents another branch of the same broad OEM configurator ecosystem and demonstrates how keymap, profile and RGB traffic can be structured.

Do not assume AULA VID/PID, commands or firmware are directly compatible with AK820 PRO.

### SinoWealth ecosystem

Open tooling groups a number of `0x258A` keyboards around SinoWealth platforms and identifies SuperFrame Phantom `258A:019D` as an SH68F90-family device.

This is useful platform-family evidence, but controller identity for the tested VOROTEX unit should still be confirmed independently before any ISP/firmware operation.

### EPOMAKER Cypher 81

Cypher 81 is a strong physical/form-factor analogue: 81-key 75% layout, TFT and encoder. The tested EPOMAKER browser hub did not offer AK820 PRO as a compatible device, so it is not currently a verified drop-in software donor.

## HID inventory priorities

### Report 9

High-priority candidate for keymap/config transport because of the exact Phantom WebHID signature match.

### Report 6

The AK820 PRO exposes an unusually large Feature Report 6. It is a candidate for larger configuration/state transfers. A possible relationship to TFT traffic is only a hypothesis until correlated with controlled observations.

### Report 5

Small Feature Report present in the descriptor. Purpose is not yet mapped.

## Safe experiment plan

1. Capture idle session.
2. Capture encoder-left session.
3. Capture encoder-right session.
4. Capture encoder-press session.
5. Diff incoming reports and report metadata.
6. Correlate physical key positions with the official AK820 PRO configuration model.
7. Map Base/FN1/FN2/Tap without sending write reports.
8. Observe official software traffic later if read-only observations are insufficient.
9. Map RGB and TFT transports separately.
10. Introduce any write test only after a reversible recovery path is documented.

## Important non-conclusions

- Same VID/PID does not mean same firmware.
- Same Feature Report ID/size does not mean same flash layout.
- Physical resemblance does not imply protocol compatibility.
- No third-party firmware should be flashed onto the VOROTEX AK820 PRO based only on these similarities.
