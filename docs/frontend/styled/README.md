# Styled componentes

Lage en stil som kan gjenbrukes på flere komponenter:

```
const IconStyle = css`
height: 1em;
width: 1em;
margin-right: ${theme.spacing('S10')};
`;

const IconAdd = styled(IconAddBase)`
${IconStyle}
`;
```
